1. `java.lang.ClassNotFoundException: javax.xml.bind.JAXBException`  
> JAXB API是java EE 的API，因此在java SE 9.0 中不再包含这个 Jar 包。 
java 9 中引入了模块的概念，默认情况下，Java SE中将不再包含java EE 的Jar包 
而在 java 6/7 / 8 时关于这个API 都是捆绑在一起的。  
解决方案：  
加入jaxb-api包