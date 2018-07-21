# ClassLoader

> https://docs.oracle.com/javase/9/docs/api/java/lang/ClassLoader.html

- A class loader is an object that is responsible for loading classes.
- A typical strategy is to transform the name into a file name and then read a "class file" of that name from a file system.
- Every Class object contains a reference to the ClassLoader that defined it.
- Class objects for array classes are not created by class loaders, but are created automatically as required by the Java runtime. The class loader for an array class, as returned by Class.getClassLoader() is the same as the class loader for its element type; if the element type is a primitive type, then the array class has no class loader.
- In addition to loading classes, a class loader is also responsible for locating resources.
- The ClassLoader class uses a delegation model to search for classes and resources. Each instance of ClassLoader has an associated parent class loader. When requested to find a class or resource, a ClassLoader instance will usually delegate the search for the class or resource to its parent class loader before attempting to find the class or resource itself.

Run-time Built-in Class Loaders

1. **Bootstrap class loader**. It is the virtual machine's built-in class loader, typically represented as null, and does not have a parent.
2. **Platform class loader**. All platform classes are visible to the platform class loader that can be used as the parent of a ClassLoader instance. Platform classes include Java SE platform APIs, their implementation classes and JDK-specific run-time classes that are defined by the platform class loader or its ancestors.
3. **System class loader**. It is also known as application class loader and is distinct from the platform class loader. The system class loader is typically used to define classes on the application class path, module path, and JDK-specific tools. The platform class loader is a parent or an ancestor of the system class loader that all platform classes are visible to it.

## 一、ClassLoader分类

### 1. Bootstrap class loader

The bootstrap class loader loads the core Java libraries[5] located in the <JAVA_HOME>/jre/lib directory. This class loader, which is part of the core JVM, is written in native code.

### 2. Extensions class loader

The extensions class loader loads the code in the extensions directories (<JAVA_HOME>/jre/lib/ext,[6] or any other directory specified by the java.ext.dirs system property). It is implemented by the sun.misc.Launcher$ExtClassLoader class.

### 3. System class loader

The system class loader loads code found on java.class.path, which maps to the CLASSPATH environment variable. This is implemented by the sun.misc.Launcher$AppClassLoader class.

### 4. User-defined class loaders

The Java class loader is written in Java. It is therefore possible to create your own class loader without understanding the finer details of the Java Virtual Machine. Every Java class loader has a parent class loader, defined when a new class loader is instantiated or set to the virtual machine's system default class loader.

This makes it possible (for example):

- to load or unload classes at runtime (for example to load libraries dynamically at runtime, even from an HTTP resource). This is an important feature for:
  - implementing scripting languages, such as Jython using bean builders
  - allowing user-defined extensibility
  - allowing multiple namespaces to communicate. This is one of the foundations of CORBA / RMI protocols for example.
- to change the way the bytecode is loaded (for example, it is possible to use encrypted Java class bytecode[7]).
- to modify the loaded bytecode (for example, for load-time weaving of aspects when using aspect-oriented programming).

## 一、ClassLoader API

### 1. getResource

此方法只能接收相对路径（例如虚拟目录resources下的文件name.txt 应该传入"name.txt"）

    public URL getResource(String name) {
        URL url;
        if (parent != null) {
            url = parent.getResource(name);
        } else {
            url = getBootstrapResource(name);
        }
        if (url == null) {
            url = findResource(name);
        }
        return url;
    }

### 2. getResourceAsStream

同样此方法也只能接收相对路径

    public InputStream getResourceAsStream(String name) {
        URL url = getResource(name);
        try {
            return url != null ? url.openStream() : null;
        } catch (IOException e) {
            return null;
        }
    }
