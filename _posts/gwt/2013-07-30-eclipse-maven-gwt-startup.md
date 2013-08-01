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


<!--more-->

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

###注意事项
>>1.eclipse gwt plugin 的compile中间件在src/main中,maven在target中，在换了jar包，中间件可能存在不兼容的错误，需要先清理
>>2.gwt 验证 jar包只能使用4.3以下。RPC接口中需要声明Support
>>3.eclipse gwt plugin 安装SDKs需要下载完整的SDK的zip
>>4.在使用泛型时，String不能正确解析,建议使用Provider包装。
>>5.引入source code 需要在xml中声明super path
>>6.maven gwt debug 运行mvn debug 然后debug as a remote java application
