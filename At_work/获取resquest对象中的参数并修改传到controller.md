# 获取resquest对象中的参数并修改传到controller

## 背景：

不同客户端登陆系统的账号不一致，所以需要将前端传过来的userId在到达controller的时候统一。

## 思路：

## 1.过滤器和拦截器的选择：

选择过滤器，先从DispatcherServlet的doDispatch方法代码解析

```java
protected void doDispatch(HttpServletRequest request, HttpServletResponse response) throws Exception {undefined
        HttpServletRequest processedRequest = request;
       ......

        try {undefined
            ModelAndView mv = null;

         ......

                // interceptor前置拦截
                if (!mappedHandler.applyPreHandle(processedRequest, response)) {undefined
                    return;
                }

                try {undefined
                    // 真正执行handler的方法，返回ModelAndView
                    mv = ha.handle(processedRequest, response, mappedHandler.getHandler());
                }
           ......
    }
```

可以看到真正传进去的是processedRequest，所以说即使在拦截器的前置拦截中将processedRequest用自定义的Request包装类代替，但是真正处理handle的时候，传进去的还是processedRequest，而不是自定义的Request包装类，所以只能在调用**doDispatch（）**之前将**HttpServletRequest**用定义的Request包装类代替，恰好过滤器可以做到。

## 2.获取到request对象的所有参数

其中GET方法的参数可以由request.getParameterMap()直接获取。POST方法的参数放在body中，没有方法能够直接取出，需要通过request的输入流去读取，即调用request.getInputStream()获取流。

request的流只允许读取一次不能重读度，所以在过滤器或拦截器里读取了request的输入流之后，请求走到controller层时就会报错。只能读一次的原因是，当调用getInputStream()方法获取输入流时得到的是一个InputStream对象，而实际类型是ServletInputStream，它继承于InputStream。InputStream的read()方法内部有一个postion，标志当前流被读取到的位置，每读取一次，该标志就会移动一次，如果读到最后，read()会返回-1，表示已经读取完了。如果想要重新读取则需要调用reset()方法，position就会移动到上次调用mark的位置，mark默认是0，所以就能从头再读了，调用reset()方法的前提是已经重写了reset()方法，当然能否reset也是有条件的，它取决于markSupported()方法是否返回true。

InputStream默认不实现reset()，并且markSupported()默认也是返回false。ServletInputStream该类没有重写mark()，reset()以及markSupported()方法。，这就是我们从request对象中获取的输入流就只能读取一次的原因。

异常信息：

```java
org.springframework.http.converter.HttpMessageNotReadableException: Required request body is missing
```

### 解决办法：

**HttpServletRequestWrapper + Filter解决输入流不能重复读取问题**

JavaEE提供了一个 HttpServletRequestWrapper类，从类名也可以知道它是一个http请求包装器，其基于装饰者模式实现了HttpServletRequest界面

```java
public class HttpServletRequestWrapper extends ServletRequestWrapper implements
        HttpServletRequest {

    /**
     * Constructs a request object wrapping the given request.
     *
     * @param request The request to wrap
     *
     * @throws java.lang.IllegalArgumentException
     *             if the request is null
     */
    public HttpServletRequestWrapper(HttpServletRequest request) {
        super(request);
    }

    private HttpServletRequest _getHttpServletRequest() {
        return (HttpServletRequest) super.getRequest();
    }

    /**
     * The default behavior of this method is to return getAuthType() on the
     * wrapped request object.
     */
    @Override
    public String getAuthType() {
        return this._getHttpServletRequest().getAuthType();
    }

    /**
     * The default behavior of this method is to return getCookies() on the
     * wrapped request object.
     */
    @Override
    public Cookie[] getCookies() {
        return this._getHttpServletRequest().getCookies();
    }
```

从源码中可以看出该类并没有真正去实现HttpServletRequest的方法，而只是在方法内又去调用HttpServletRequest的方法，所以我们可以通过继承该类并实现想要重新定义的方法以达到包装原生HttpServletRequest对象的目的。

首先我们要定义一个容器，将输入流里面的数据存储到这个容器里，这个容器可以是数组或集合。然后我们重写getInputStream()方法和getReader()方法，每次都从这个容器里读数据，这样我们的输入流就可以读取任意次了。

### 代码实现：

```java
  /**
 * HttpServletRequest包装类,目的让其输入流可以重复读,获取body
 */
@Slf4j
public class GetRequestBodyWrapper extends HttpServletRequestWrapper {

    // 存储body数据的容器
    private final byte[] body;

    public GetRequestBodyWrapper(HttpServletRequest request) throws IOException {
        super(request);
        // 将body数据存储起来
        String bodyStr = getBodyString(request);
        body = bodyStr.getBytes(Charset.defaultCharset());
    }

    /**
     * 获取请求Body
     * @param request request
     * @return String
     */
    public String getBodyString(final ServletRequest request) {
        try {
            return inputStream2String(request.getInputStream());
        } catch (IOException e) {
            log.error("", e);
            throw new RuntimeException(e);
        }
    }

    /**
     * 获取请求Body
     * @return String
     */
    public String getBodyString() {
        final InputStream inputStream = new ByteArrayInputStream(body);
        return inputStream2String(inputStream);
    }

    /**
     * 将inputStream里的数据读取出来并转换成字符串
     * @param inputStream inputStream
     * @return String
     */
    private String inputStream2String(InputStream inputStream) {
        StringBuilder sb = new StringBuilder();
        BufferedReader reader = null;
        try {
            reader = new BufferedReader(new InputStreamReader(inputStream, Charset.defaultCharset()));
            String line;
            while ((line = reader.readLine()) != null) {
                sb.append(line);
            }
        } catch (IOException e) {
            log.error("", e);
            throw new RuntimeException(e);
        } finally {
            if (reader != null) {
                try {
                    reader.close();
                } catch (IOException e) {
                    log.error("", e);
                }
            }
        }
        return sb.toString();
    }

    @Override
    public BufferedReader getReader() throws IOException {
        return new BufferedReader(new InputStreamReader(this.getInputStream()));
    }

    @Override
    public ServletInputStream getInputStream() throws IOException {

        final ByteArrayInputStream inputStream = new ByteArrayInputStream(body);

        return new ServletInputStream() {
            @Override
            public int read() throws IOException {
                return inputStream.read();
            }
            @Override
            public boolean isFinished() {
                return false;
            }
            @Override
            public boolean isReady() {
                return false;
            }
            @Override
            public void setReadListener(ReadListener readListener) {
                log.info("--------------------RequestWrapper ReadListener:{} ",this);
            }
        };
    }

    @Override
    public Enumeration<String> getHeaderNames(){
        return super.getHeaderNames();
    }
    
    @Override
    public Enumeration<String> getHeaders(String name){
        return super.getHeaders(name);
    }
}
```

## 3.修改request对象中的参数

### 修改GET方法的参数

#### 代码实现：

```JAVA
**
 * 修改request的GET方法参数
 */
public class UpdateParamWrapper extends HttpServletRequestWrapper {

    // 修改的参数的容器
    private Map<String, String[]> params = new HashMap();

    /**
     * Constructs a request object wrapping the given request.
     *
     * @param request The request to wrap
     * @throws IllegalArgumentException if the request is null
     */
    public UpdateParamWrapper(HttpServletRequest request,Map<String, Object> extendParams) {
        super(request);
        // 修改参数
        addAllParameters(extendParams);
    }

    /**
     * 添加所有参数
     * @param otherParams
     */
    private void addAllParameters(Map<String, Object> otherParams) {
        for (Map.Entry<String, Object> entry : otherParams.entrySet()) {
            addParameter(entry.getKey(), entry.getValue());
        }
    }

    private void addParameter(String name, Object value) {
        if (value != null) {
            if (value instanceof String[]) {
                params.put(name, (String[]) value);
            } else if (value instanceof String) {
                params.put(name, new String[]{(String) value});
            } else {
                params.put(name, new String[]{String.valueOf(value)});
            }
        }
    }

    @Override
    public Enumeration getParameterNames() {
        return new Vector(params.keySet()).elements();
    }

    @Override
    public String getParameter(String name) {
        String[] values = params.get(name);
        if (values == null || values.length == 0) {
            return null;
        }
        return values[0];
    }

    @Override
    public Map<String,String[]> getParameterMap(){
        return params;
    }

    @Override
    public String[] getParameterValues(String name) {
        return params.get(name);
    }
}
```

注意：一定要重写getParameterValues(String name)方法

如果不写的话，虽然修改了request，但Controller中获取到的参数，还是原来的request上的参数。

跟踪执行代码发现，当走到String[] paramValues = webRequest.getParameterValues(name)；这块代码的时候，真正实现的竟然是Tomcat容器提供的request包装类中的org.apache.catalina.connector.RequestFacade中的方法

因为因为这里的WebUtils.getNativeRequest(servletRequest, MultipartHttpServletRequest.class)最后返回了一个Tomcat提供的request包装类。

```java
 @Override
public String[] getParameterValues(String name) {

    if (request == null) {
        throw new IllegalStateException(
                        sm.getString("requestFacade.nullRequest"));
    }

    String[] ret = null;
    if (SecurityUtil.isPackageProtectionEnabled()){
        ret = AccessController.doPrivileged(
            new GetParameterValuePrivilegedAction(name));
        if (ret != null) {
            ret = ret.clone();
        }
    } else {
        ret = request.getParameterValues(name);
    }

    return ret;
}
```

解决的办法是自定义包装类中（继承了ServletRequestWrapper）重写 getParameterValues(String name)方法。我觉得是因为没有重写造成用的是tomcat提供的包装类。

### 修改POST方法的参数

```java
/**
 * 重写request,获取body数据的时候读取新的body
 */
public class UpdateRequestBodyWrapper extends HttpServletRequestWrapper {

    // 重新赋值的body赋值
    private String bodyJsonStr;

    /**
     * @param request The request to wrap
     * @throws IllegalArgumentException if the request is null
     */
    public UpdateRequestBodyWrapper(HttpServletRequest request, String bodyJson) {
        super(request);
        this.bodyJsonStr = bodyJson;
    }

    @Override
    public ServletInputStream getInputStream() throws IOException {
        if (Strings.isEmpty(bodyJsonStr)){
            bodyJsonStr = "";
        }
        // 指定utf-8编码
        final ByteArrayInputStream inputStream = new ByteArrayInputStream(bodyJsonStr.getBytes(StandardCharsets.UTF_8));
        return new ServletInputStream() {
            @Override
            public int read() throws IOException {
                return inputStream.read();
            }
            @Override
            public boolean isFinished() {
                return false;
            }
            @Override
            public boolean isReady() {
                return false;
            }
            @Override
            public void setReadListener(ReadListener readListener) {
            }
        };
    }

    @Override
    public BufferedReader getReader() throws IOException {
        return new BufferedReader(new InputStreamReader(this.getInputStream()));
    }

    public String getBodyJsonStr() {
        return bodyJsonStr;
    }

    public void setBodyJsonStr(String bodyJsonStr) {
        this.bodyJsonStr = bodyJsonStr;
    }
}
```

## 4.编写过滤器

```java
@Slf4j
@Component
public class ReplaceStreamFilter implements Filter {

    private final static String TK = "TK";
    private final static String USERID = "userId";
    private final static String FCU = "fcu";

    @Resource
    private UserMapper userMapper;

    @Override
    public void init(FilterConfig filterConfig) throws ServletException {
    }

    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {

        try {
            HttpServletRequest req = (HttpServletRequest) request;
            String method = req.getMethod();

            if (ObjectUtil.equal(method,"GET")){
                chain.doFilter(GetRequest(req),response);
            }else if (ObjectUtil.equal(method,"POST")){
                chain.doFilter(PostRequest(req),response);
            }
        }catch (Exception e){
            log.info(e.toString());
        }
    }

    public HttpServletRequest GetRequest(HttpServletRequest request){
        Map<String, String[]> parameterMap = request.getParameterMap();
        Map<String, Object> extendParams = new HashMap<>(parameterMap);
        log.info("传入的请求参数：{}",parameterMap.toString());
        String userId = Arrays.toString(parameterMap.get(USERID))
                .replace("[","").replace("]","");
        String fcu = Arrays.toString(parameterMap.get(FCU))
                .replace("[","").replace("]","");
        if (userId.contains(TK)){
            extendParams.put(USERID, userMapper.queryOAByTK(userId));
        }
        if (fcu.contains(TK)){
            extendParams.put(FCU, new String[]{userMapper.queryOAByTK(fcu)});
        }
        log.info("转换后的请求参数：{}",extendParams.toString());
        return new UpdateParamWrapper(request, extendParams);
    }

    public HttpServletRequest PostRequest(HttpServletRequest request) throws IOException {
        Map<String, Object> extendParams = new HashMap<>();
        GetRequestBodyWrapper requestWrapper = new GetRequestBodyWrapper(request);
        String bodyString = requestWrapper.getBodyString();
        JSONObject jsonObject = JSON.parseObject(bodyString);
        // 放入新的map中，用于修改
        jsonObject.forEach(extendParams::put);
        log.info("传入的参数：{}",jsonObject);
        Object j1 = jsonObject.get(USERID);
        Object j2 = jsonObject.get(FCU);
        if (j1 != null){
            String userId = j1.toString();
            if (userId.contains(TK)){
                extendParams.put(USERID,userMapper.queryOAByTK(userId));
            }
        }
        if (j2 != null){
            String fcu = j2.toString();
            if (fcu.contains(TK)){
                extendParams.put(FCU,userMapper.queryOAByTK(fcu));
            }
        }
        log.info("修改后的参数：{}",extendParams);
        String s = JSONObject.toJSONString(extendParams);
        return new UpdateRequestBodyWrapper(requestWrapper, s);
    }

    @Override
    public void destroy() {
    }
}
```



