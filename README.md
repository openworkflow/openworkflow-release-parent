openworkflow-release-parent
======================

Pom which can be inherited for OpenWorkflow releases defining some common release properties.

Usage
-----

Inherit the openworkflow-release-parent pom inside your project like so  
  
    <parent>
      <groupId>co.openworkflow</groupId>
      <artifactId>openworkflow-release-parent</artifactId>
      <version>${LATEST_VERSION}</version>
      <!-- do not remove empty tag - http://jira.codehaus.org/browse/MNG-4687 -->
     <relativePath />
    </parent>  
    
If you have a multi-module build, just inherit in your parent pom.  

Specify the <scm> section for your project eg.
    
    <scm>
      <url>https://github.com/openworkflow/MY_PROJECT_URL</url>
      <connection>scm:git:git@github.com:openworkflow/MY_PROJECT_URL.git</connection>
      <developerConnection>scm:git:git@github.com:openworkflow/MY_PROJECT_URL.git</developerConnection>
    </scm>

Release
-------

Prerequisite:  

  Add the following to the profiles and servers section of your local settings.xml file. This allows the signing of your artifacts during the release. <strong>Mandatory for maven central</strong>.
  
    <settings>
      ...
      
      <servers>
        ...
        
        <server>
          <id>central</id>
          <username>MY_CENTRAL_USER</username>
          <password>MY_CENTRAL_PASSWORD</password>
        </server>
        
      </servers>
      
      
      <profiles>
        ...
        
        <profile>
          <id>sonatype-oss-release</id>
          <properties>
            <gpg.passphrase>YOUR_SECRET_GPG_PASSPHRASE</gpg.passphrase>
          </properties>
        </profile>
        
      </profiles>
    </settings>

To release your own project use the following command:

    mvn \
    release:prepare release:perform \
    -B -Dresume=false -Dtag=1.0.0 -DreleaseVersion=1.0.0 -DdevelopmentVersion=1.1.0-SNAPSHOT \
    -Darguments="--settings=${PATH_TO_YOUR_SETTINGS_XML_FILE}" \
    --settings=${PATH_TO_YOUR_SETTINGS_XML_FILE}
    
This will trigger the `sonatpye-oss-release` profile inside the `openworkflow-release-parent` pom automatically.
    
Customization
-------------

You can override some default behaviours through the usage of the command line properties below. Add those properties to the `-Darguments="INSERT_PROPERTIES_HERE"` part of the Maven release command.

<table>
  <tr>
    <th>Property</th><th>Description</th>
  </tr>
  <tr>
    <td>skip.central.release</td><td>When setting the value to true, skip the deployment to maven central.<br/>Default is <strong>false</strong>.</td>
  </tr>
  <tr>
    <td>serverId</td><td>Points to the corresponding entry in your settings.xml like <server><id>yourServerId</id></server> to specify your login credentials.<br/>Default is <strong>central</strong> for maven central.</td>
  </tr>
</table>

Testing
-------

You can test your release steps by using the following Maven release command:

    mvn \
    release:prepare release:perform \
    -B -Dresume=false -Dtag=1.0.0 -DreleaseVersion=1.0.0 -DdevelopmentVersion=1.1.0-SNAPSHOT \
    -Darguments="--settings=${PATH_TO_YOUR_SETTINGS_XML_FILE} -Dskip.central.release=true -Dnexus.release.repository=${URL_OF_YOUR_LOCAL_NEXUS_REPOSITORY}" \
    --settings=${PATH_TO_YOUR_SETTINGS_XML_FILE} \
    -DpushChanges=false -DremoteTagging=false -DlocalCheckout=true
    
This will do a release but will not push anything to the involved git repositories and will not deploy to Maven Central. Instead it will deploy to a local Nexus repository. (Hint: You can use a simple Nexus Docker image to do so.)
