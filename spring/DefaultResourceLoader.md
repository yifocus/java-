# DefaultResourceLoader

![](images\DefaultResourceLoader.png)





**请求访问文件资源接口**

**１、Resource接口定义了应用访问底层资源的能力。**

- 通过FileSystemResource以文件系统绝对路径的方式进行访问；
- 通过ClassPathResource以类路径的方式进行访问；
- 通过ServletContextResource以相对于Web应用根目录的方式进行访问。

　　在获取资源后，用户就可以通过Resource接口定义的多个方法访问文件的数据和其他的信息：如可以通过getFileName()获取文件名，通过getFile()获取资源对应的File对象，通过getInputStream()直接获取文件的输入流。此外，还可以通过createRelative(String relativePath)在资源相对地址上创建新的文件。

**２、ResourceLoader接口提供了一个加载文件的策略。它提供了一个默认的实现类DefaultResourceLoader，获取资源代码如下：**



```java
@Override
public Resource getResource(String location) {
    Assert.notNull(location, "Location must not be null");
    if (location.startsWith("/")) {
        return getResourceByPath(location);
    }
    else if (location.startsWith(CLASSPATH_URL_PREFIX)) {
        return new ClassPathResource(location.substring(CLASSPATH_URL_PREFIX.length()), getClassLoader());
    }
    else {
        try {
            // Try to parse the location as a URL...
            URL url = new URL(location);
            return new UrlResource(url);
        }
        catch (MalformedURLException ex) {
            // No URL -> resolve as resource path.
            return getResourceByPath(location);
        }
    }
}
```





```java
 protected Resource getResourceByPath(String path) {
     return new ClassPathContextResource(path, getClassLoader());
 }
```




大牛博客参考
http://www.tianxiaobo.com/
