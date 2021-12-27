# java实现手动分页

创建返回前端的对象实体：

```java
@Data
@NoArgsConstructor
@AllArgsConstructor
public class PageBean<T> {

    /**
     * 存放需要返回的实体类数据
     */
    private List<T> lists;
    /**
     * 当前页码数  需要传参
     */
    private Integer pageNo;
    /**
     * 每页显视的行数  需要传参
     */
    private Integer pageSize;
    /**
     * 总页数
     */
    private Integer totalPage;
    /**
     * 总行数
     */
    private Integer rows;
    
}
```

创建手动分页工具类：

```java
public static <T>PageBean getPage(List<T> lists,Integer pageNo,Integer pageSize){

    PageBean pageBean = new PageBean<>();

    // 起始截取数据位置
    int startNum = (pageNo - 1) * pageSize + 1;
    if (startNum > lists.size()) {
        return null;
    }
    List<T> res = new ArrayList<>();
    int rum = lists.size() - startNum;
    if (rum < 0) {
        return null;
    }
    // 截取数据正好是最后一个
    if (rum == 0) {
        int index = lists.size() - 1;
        res.add(lists.get(index));
        pageBean.setLists(res);
        return pageBean;
    }
    //剩下的数据还够1页，返回整页的数据
    if (rum / pageSize >= 1) {
        //截取从startNum开始的数据
        for (int i = startNum; i < startNum + pageSize; i++) {
            res.add(lists.get(i - 1));
        }
        pageBean.setLists(res);
        return pageBean;
        //不够一页，直接返回剩下数据
    } else if ((rum / pageSize == 0) && rum > 0) {
        for (int j = startNum; j <= lists.size(); j++) {
            res.add(lists.get(j - 1));
        }
        pageBean.setLists(res);
        return pageBean;
    } else {
        return null;
    }
```

引入泛型也要先声明；

方法返回值前面加泛型，该方法就是泛型方法，表示传入参数有泛型