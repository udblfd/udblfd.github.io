经常会有童鞋把 Map 和 Foreach 用错，可能会出现如下用法：

```
List<Student> studentChangeList = studentList.stream()
        .forEach(student -> student.setAge(99));
```

有些编译器会直接报错，比如 IDEA。因为 ForEach 是没有返回值的，ForEach 处理过的 Stream 是无法再赋值给 studentChangeList 。

但是现在就是需要对集合进行处理，并获取处理过的集合数据，这时候可以这样做

```
studentList.stream().forEach(student -> student.setAge(99));
```

studentList 中的数据就是已经处理过的数据。

**下面就 Map 和 ForEach 做一些简单的说明，大体上就能对这两个方法有所理解**

#### 一、 结论
Map：返回的是一个新流，可以对这个流进一步操作

ForEach：返回void，即无返回值

#### 二、源码

**1. Map 源码说明**

```java

    /**
     * Returns a stream consisting of the results of applying the given
     * function to the elements of this stream.
     *
     * <p>This is an <a href="package-summary.html#StreamOps">intermediate
     * operation</a>.
     *
     * @param <R> The element type of the new stream
     * @param mapper a <a href="package-summary.html#NonInterference">non-interfering</a>,
     *               <a href="package-summary.html#Statelessness">stateless</a>
     *               function to apply to each element
     * @return the new stream
     */
    <R> Stream<R> map(Function<? super T, ? extends R> mapper);
```

**2. ForEach 源码说明**

```java

    /**
     * Performs an action for each element of this stream.
     *
     * <p>This is a <a href="package-summary.html#StreamOps">terminal
     * operation</a>.
     *
     * <p>The behavior of this operation is explicitly nondeterministic.
     * For parallel stream pipelines, this operation does <em>not</em>
     * guarantee to respect the encounter order of the stream, as doing so
     * would sacrifice the benefit of parallelism.  For any given element, the
     * action may be performed at whatever time and in whatever thread the
     * library chooses.  If the action accesses shared state, it is
     * responsible for providing the required synchronization.
     *
     * @param action a <a href="package-summary.html#NonInterference">
     *               non-interfering</a> action to perform on the elements
     */
    void forEach(Consumer<? super T> action);
```
#### 三、应用
1.Map 是1对1的映射

示例：
```
    public static void main(String[] args) {
        List<Student> studentList = new ArrayList<>();
        Student student1 = new Student();
        student1.setStudentId(1);
        student1.setStudentName("李毅");
        student1.setAge(17);
        Student student2 = new Student();
        student2.setStudentId(2);
        student2.setStudentName("张三丰");
        student2.setAge(18);
        studentList.add(student1);
        studentList.add(student2);
        List<Integer> ageList = studentList.stream()
                .map(student -> student.getAge() + 100)
                .collect(Collectors.toList());
        ageList.stream().forEach(System.out::println);

    }
```
运行结果：

```
117
118

```


2.ForEach 是对 Stream 中每一个元素进行处理。

虽然 ForEach 处理 Stream 中元素的时候没有返回值，但是 ForEach 对 Stream 中元素已经产生影响，即 ForEach 对 Stream 中元素的操作已经被保存下来。

示例：

```java
    public static void main(String[] args) {
        List<Student> studentList = new ArrayList<>();
        Student student1 = new Student();
        student1.setStudentId(1);
        student1.setStudentName("李毅");
        student1.setAge(17);
        Student student2 = new Student();
        student2.setStudentId(2);
        student2.setStudentName("张三丰");
        student2.setAge(18);
        studentList.add(student1);
        studentList.add(student2);
        studentList.stream().forEach(student -> {
            student.setAge(99);
            student.setStudentName("英俊");
        });
        studentList.stream().forEach(System.out::println);
    }
```
运行结果：

```
Student(studentId=1, studentName=英俊, age=99)
Student(studentId=2, studentName=英俊, age=99)
```

