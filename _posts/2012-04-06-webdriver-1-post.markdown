---
layout: post
title: "WebDriver 1: Set up WebDriver using Maven3"
category: WebDriver
comments: true
---
<p>
<h3>1. Learn Maven3 first.</h3>
  This is quite simple. Go through <a href="http://maven.apache.org/guides/getting-started/maven-in-five-minutes.html">Maven in 5 Minutes</a>
  That's quite enough.
</p>

<p>
<h3>2. Set up project</h3>
Using the command in Maven in 5 Minutes to create a project using your own groupId and artifactId.
<pre class="prettyprint">
mvn archetype:generate -DgroupId=com.ahchoo.automation -DartifactId=ahcoo -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
</pre>
</p>

<p>
Change the pom.xml to add Junit and WebDriver dependencies
<pre class="prettyprint">
&lt;project&gt;
...
    &lt;dependencies&gt;
        &lt;dependency&gt;
            &lt;groupId&gt;junit&lt;/groupId&gt;
            &lt;artifactId&gt;junit&lt;/artifactId&gt;
            &lt;version&gt;4.10&lt;/version&gt;
            &lt;scope&gt;test&lt;/scope&gt;
        &lt;/dependency&gt;
        &lt;dependency&gt;
            &lt;groupId&gt;org.seleniumhq.selenium&lt;/groupId&gt;
            &lt;artifactId&gt;selenium-java&lt;/artifactId&gt;
            &lt;version&gt;2.20.0&lt;/version&gt;
            &lt;/dependency&gt;
            &lt;/dependencies&gt;&lt;/scope&gt;&lt;/version&gt;&lt;/artifactId&gt;&lt;/groupId&gt;
        &lt;/dependency&gt;
    &lt;/dependencies&gt;
...
&lt;/project&gt;
</pre>
Then run <code class="cmd">mvn clean install</code>
</p>

<p>
<h3>3. Using eclipse to import this project</h3>
install <a href="http://eclipse.org/m2e/">m2eclipse</a> and
run command in the project root directory:
<pre class="prettyprint">
mvn eclipse:clean
mvn eclipse:eclipse
</pre>

open eclise, click File and choose Import:
<p>
    <img src="/photos/1.png">
</p>
Choose Existing Projects into Workspace
<p>
    <img src="/photos/2.png">
</p>

After import, you will see the file tree in eclipse project explorer:
<p>
<img src="/photos/3.png">
</p>
</p>

Thus the environment is set up.




