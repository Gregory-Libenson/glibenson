# ESDRT


## Requirements
- Java 8 (or later)
- CVS
- Maven
- Tomcat 8 (or later)

## Eclipse
If you use Eclipse, you will also likely need the following plug-ins. Note that Eclipse is not required. You can do a full compile and build a WAR file just from the command line. These POlkug-ins are required to use CVS, edit Groovy, and edit Jasper reports. 

- CVS Plug-in
- Jasper Plug-in
- Groovy Plug-in
 

## Build the project
`cd ESDRT`


Non-maven jars must be installed to your repository. To do this use Maven initialize.


`mvn initialize`


Command for eclipse build path entries:

`mvn eclipse:eclipse`

Build project:

`mvn clean install`


To run the project within Eclipse, you'll need to do the following: 


For Groovy support in Eclipse, use the following as a new software source and install all components. This may need to be changed in the future when the plug-in hits general availability.

`http://dist.springsource.org/release/GRECLIPSE/e4.7/`

Note that this version is for Eclipse Oxygen, version 4.7. Make sure to match the plugin to your version of Eclipse.

You may also need to set the Groovy compiler version to match that in the project's Maven file, currently 2.4. 

In deployment assemblies (project -> properties)
"Add" -> "java build path entries"
select all, okay it

"Add" -> Folder, then select target/ESDRT/WEB-INF/classes/reports, and deploy it to WEB-INF/classes/reports. You'll need to double-click the "Deploy Path" value in the table of deployments to change the value from its default.

Create a Tomcat 8 (or later) server and add the `ojdbc6.jar` to its boot classpath.

## Configuration
The datasource used for the system must be defined in the Tomcat `server.xml`, as must the configuration for the form based authentication mechanism. There is a sample `server.xml` file in the root directory of the project. The basic steps required to a stock sever.xml are:

* Add a datasource resource called `jdbc/esdrt` in the `GlobalNamingResources` section
* After ESDRT has been added as a web application, it should be listed in the `server.xml` as a `Context`. Expand this context with a security realm. The first example is for a Tomcat 8.0 or earlier, and the second is for Tomcat 8.5 and later.

```xml
<Realm className="org.apache.catalina.realm.LockOutRealm">
	<Realm	className="org.apache.catalina.realm.DataSourceRealm"
			dataSourceName="jdbc/esdrt"
			digest="SHA1"
			roleNameCol="rolename"
			userCredCol="PasswordHash"
			userNameCol="Application_User_ID"
			userRoleTable="Application_Role"
			userTable="Application_User"/>
</Realm>

<Realm className="org.apache.catalina.realm.DataSourceRealm"
	<Realm className="org.apache.catalina.realm.LockOutRealm">
		dataSourceName="jdbc/esdrt"
		localDataSource="true"
		userTable="Application_User"
		userNameCol="Application_User_ID"
		userCredCol="PasswordHash"
		userRoleTable="Application_Role"
		roleNameCol="rolename">
		<CredentialHandler
			className="org.apache.catalina.realm.MessageDigestCredentialHandler"
			algorithm="SHA-1"/>
		</Realm>
	</Realm>						
```
* Make sure that the closing tag of the Context is *after* the end of the Realm, and that the Host closing tag is after it as well.


## Project Files and CVS
Please don't check in any Eclipse (or other IDE) configuration files, such as `.settings`, `.project`, or `.classpath`.  Another developer may be using a different version of Eclipse, in a different location, or even a completely different IDE.  These files just clutter CVS causing other file changes to be missed.  Thanks.