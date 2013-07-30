---
layout: post
category : java
tags : [gwt,eclipse,maven]
---
{% include JB/setup %}
###安装google gwt plugin
>update site http://dl.google.com/eclipse/plugin/${eclipse.version}
###建立maven gwt project
	mvn archetype:generate    -DarchetypeGroupId=org.codehaus.mojo    -DarchetypeArtifactId=gwt-maven-plugin    -DarchetypeVersion=2.5.1
###修改project的pom.xml
	<pluginManagement>
	        <plugins>
	            <!--This plugin's configuration is used to store Eclipse m2e settings 
	                only. It has no influence on the Maven build itself. -->
	            <plugin>
	                <groupId>org.eclipse.m2e</groupId>
	                <artifactId>lifecycle-mapping</artifactId>
	                <version>1.0.0</version>
	                <configuration>
	                    <lifecycleMappingMetadata>
	                        <pluginExecutions>
	                            <pluginExecution>
	                                <pluginExecutionFilter>
	                                    <groupId>org.codehaus.mojo</groupId>
	                                    <artifactId>
	                                        gwt-maven-plugin
	                                    </artifactId>
	                                    <versionRange>
	                                        [2.5.1,)
	                                    </versionRange>
	                                    <goals>
	                                        <goal>i18n</goal>
	                                    </goals>
	                                </pluginExecutionFilter>
	                                <action>
	                                    <ignore></ignore>
	                                </action>
	                            </pluginExecution>
	                        </pluginExecutions>
	                    </lifecycleMappingMetadata>
	                </configuration>
	            </plugin>
	        </plugins>
	    </pluginManagement>
###配置eclipse gwt plugin
>google web toolkit 中rmove junit module
###备注
>[eclipse no maven plugins](http://wiki.eclipse.org/M2E_plugin_execution_not_covere)

>[spring source github](https://github.com/SpringSource)



<!--more-->
