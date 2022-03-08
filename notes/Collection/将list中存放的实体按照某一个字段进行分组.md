# 将list中存放的实体按照某一个字段进行分组

```java
// 将list中存放的实体按照某一个字段进行分组
public static Map<String,List<String>> grouping(List<Student> studentList){

    Map<String, List<String>> resultMap = new HashMap<>();

    for (Student student : studentList) {
        String sex = student.getSex();
        String name = student.getName();

        if (resultMap.containsKey(sex)){
            resultMap.get(sex).add(name);
        }else {
            // 如果key不存在，新建key
            List<String> list = new ArrayList<>();
            list.add(name);
            resultMap.put(sex, list);
        }
    }
    return resultMap;
}
```