# JSON、JSONObject和JSONArray

## JSON

JSON 就是一种轻量级的数据交换格式，是一种取代[XML](https://so.csdn.net/so/search?q=XML&spm=1001.2101.3001.7020)的数据结构。

### 优势

JSON 的简洁 和 清晰的层次结构

易于人阅读和编写，同时也易于机器解析和生成

有效的提升网络传输效率

支持多种语言，很多流行的语言都对 JSON 格式有着很友好的支持

### JSON对象格式

```java
{

"area": "河北承德",

"name": "沃尼蒂",

"age": 25

}
```

### JSONObject

JSONObject 是根据 JSON 形式在 Java 中存在的对象映射。

各大 JSON 类库的 JSONObject 内部实现也是不太一样的。这里以 fastjson 举例。

```java
public class JSONObject extends JSON implements Map<String, Object>, Cloneable, Serializable {

    private static final long         serialVersionUID         = 1L;
    private static final int          DEFAULT_INITIAL_CAPACITY = 16;

    private final Map<String, Object> map;

    public JSONObject(){
        this(DEFAULT_INITIAL_CAPACITY, false);
    }

    public JSONObject(Map<String, Object> map){
        if (map == null) {
            throw new IllegalArgumentException("map is null.");
        }
        this.map = map;
    }

    public JSONObject(boolean ordered){
        this(DEFAULT_INITIAL_CAPACITY, ordered);
    }

    public JSONObject(int initialCapacity){
        this(initialCapacity, false);
    }

    public JSONObject(int initialCapacity, boolean ordered){
        if (ordered) {
            map = new LinkedHashMap<String, Object>(initialCapacity);
        } else {
            map = new HashMap<String, Object>(initialCapacity);
        }
    }
    ............
}
```

可以看出就是对 HashMap 的一层封装，并提供了一些个性化方法。

### JSONArray

顾名思义是由JSONObject构成的数组，用[]表示，里面可以套上JSON，例如：[ {“name”: “Michael”}, {“name”: “Jake”}]。

以 fastjson 举例

```java
public class JSONArray extends JSON implements List {
    private List list = new ArrayList();

    public JSONArray() {

    }

    public JSONArray(List list) {
        this.list = list;
    }

    public JSONObject getJSONObject(int index) {
        Object value = list.get(index);

        if (value instanceof JSONObject) {
            return (JSONObject) value;
        }

        if (value instanceof Map) {
            return new JSONObject((Map) value);
        }

        if (value instanceof String) {
            return JSON.parseObject((String) value);
        }
        ............
    }
```

相当于 List 中 套了个 Map 类结构