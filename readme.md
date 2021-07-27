# KXML2 for Kik

This is designed for XMPP parsing / serializing for Kik specifically. Don't use for anything else. 

This is also *not* stable as breaking changes can be made to it at any time.

Example:

```java
    public static XmlPullParser getNodeParser(String data) throws IOException, XmlPullParserException {
        KXmlParser parser = new KXmlParser();
        parser.setFeature("http://xmlpull.org/v1/doc/features.html#relaxed", true);
        parser.setInput(new StringReader(data));
        parser.next();
        return parser;
    }
```

Gradle file:

```groovy
repositories {
    mavenCentral()
    maven {
        url "https://jitpack.io" // maven repo where the current library resides
    }
    maven {
        url "https://dl.bintray.com/unverbraucht/java9-fixed-jars" // repo for fetching `xmlpull` dependency that's java 9 enabled
    }
    maven {
        url 'https://gitlab.com/api/v4/projects/26729549/packages/maven' // bluemods: since bintray is gone, we now need this dependency
    }
}

dependencies {
    compile 'org.xmlpull:xmlpull:1.1.4.0'
    compile 'com.github.bluemods:kxml2:-SNAPSHOT'
}
```
