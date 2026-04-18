# KXML2 for Kik

This is designed and used in production for XMPP parsing / serializing for Kik clients specifically. It is not recommended for use in other scenarios.

It's advised not to use XmlPullParserFactory, as it's very slow compared to initializing the KXmlParser directly (201ms vs 5285ms for 100,000 loops)

Example (parsing):

```java
    public static XmlPullParser getNodeParser(String stanza) throws IOException, XmlPullParserException {
        KXmlParser parser = new KXmlParser();
        // Kik is very lenient with XMPP, always set relaxed to true
        parser.setFeature("http://xmlpull.org/v1/doc/features.html#relaxed", true);
        parser.setInput(new StringReader(stanza));
        parser.next();
        return parser;
    }
```

Example (writing):

```java
    public void requestRoster() {
        try {
            StringWriter writer = new StringWriter();

            KXmlSerializer serializer = new KXmlSerializer();
            serializer.setOutput(writer);

            serializer.startTag(null, "iq");
            serializer.attribute(null, "type", "get");
            serializer.attribute(null, "id", UUID.randomUUID().toString());

            serializer.startTag(null, "query");
            serializer.attribute(null, "p", "8");
            serializer.attribute(null, "xmlns", "jabber:iq:roster");

            serializer.endTag(null, "query");
            serializer.endTag(null, "iq");

            // you must flush the StringWriter before calling toString() on it
            writer.flush();

            // The OutputStream instance that writes out to Kik's XMPP server
            OutputStream os = (...);

            // the stanza we are sending
            String stanza = writer.toString();

            System.out.println("[+] WRITING: " + stanza);

            // Make sure to use StandardCharsets.UTF_8
            // otherwise unicode text like emojis won't send properly
            os.write(stanza.getBytes(StandardCharsets.UTF_8));
            
            // flush the buffer, forcing it to be written out
            os.flush();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
```

Gradle (Groovy):

```groovy
repositories {
    mavenCentral()
    maven {
        // Leave your other repositories the same, add this line if missing:
        url "https://jitpack.io"
    }
}

dependencies {
    implementation("com.github.bluemods:kxml2:3.0.0")
}
```


Gradle (Kotlin) 

`settings.gradle.kts`:
```kotlin
dependencyResolutionManagement {
    repositoriesMode.set(RepositoriesMode.FAIL_ON_PROJECT_REPOS)
    repositories {
        // Leave your other repositories the same, add this line if missing:
        maven { setUrl("https://jitpack.io") }
    }
}
```

`build.gradle.kts`
```kotlin
dependencies {
    // Kik XMPP parser
    implementation("com.github.bluemods:kxml2:3.0.0")
}
```