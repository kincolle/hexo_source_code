---
title: Config
date: 2018-07-18 19:19:25
tags:
categories:
- Mybatis
- Config
---

## Introduction
We use SqlSessionFactoryBuilder to build SqlSessionFactory, so let's see how it works


	public class SqlSessionFactoryBuilder {
	
	  //Reader will read mybatis config file
	  //besides Reader, inputStream is another choice
	  public SqlSessionFactory build(Reader reader) {
	    return build(reader, null, null);
	  }
	
	  public SqlSessionFactory build(Reader reader, String environment) {
	    return build(reader, environment, null);
	  }
	  
	  public SqlSessionFactory build(Reader reader, Properties properties) {
	    return build(reader, null, properties);
	  }
	  
	  //get mybatis config by using XMLConfigBuilder, then build SqlSessionFactory instance
	  public SqlSessionFactory build(Reader reader, String environment, Properties properties) {
	    try {
	      XMLConfigBuilder parser = new XMLConfigBuilder(reader, environment, properties);
	      return build(parser.parse());
	    } catch (Exception e) {
	      throw ExceptionFactory.wrapException("Error building SqlSession.", e);
	    } finally {
	      ErrorContext.instance().reset();
	      try {
	        reader.close();
	      } catch (IOException e) {
	      }
	    }
	  }
	
	  public SqlSessionFactory build(Configuration config) {
	    return new DefaultSqlSessionFactory(config);
	  }
	
	}

Through this source codel, we know we set config of mybatis into SqlSessionFactoryBuilder by using XMLConfigBuilder. Then let's see the source code of XMLConfigBuilder:  

	public class XMLConfigBuilder extends BaseBuilder {
	  public XMLConfigBuilder(InputStream inputStream, String environment, Properties props) {
	    this(new XPathParser(inputStream, true, props, new XMLMapperEntityResolver()), environment, props);
	  }
	
	  private XMLConfigBuilder(XPathParser parser, String environment, Properties props) {
	    super(new Configuration());
	    ErrorContext.instance().resource("SQL Mapper Configuration");
	    this.configuration.setVariables(props);
	    this.parsed = false;
	    this.environment = environment;
	    this.parser = parser;
	  }
	  
	  public Configuration parse() {
	    if (parsed) {
	      throw new BuilderException("Each XMLConfigBuilder can only be used once.");
	    }
	    parsed = true;
	    //root node configuration
	    parseConfiguration(parser.evalNode("/configuration"));
	    return configuration;
	  }
	
	  private void parseConfiguration(XNode root) {
	    try {
	      propertiesElement(root.evalNode("properties")); 
	      typeAliasesElement(root.evalNode("typeAliases"));
	      pluginElement(root.evalNode("plugins"));
	      objectFactoryElement(root.evalNode("objectFactory"));
	      objectWrapperFactoryElement(root.evalNode("objectWrapperFactory"));
	      settingsElement(root.evalNode("settings"));
	      environmentsElement(root.evalNode("environments"));
	      databaseIdProviderElement(root.evalNode("databaseIdProvider"));
	      typeHandlerElement(root.evalNode("typeHandlers"));
	      mapperElement(root.evalNode("mappers"));
	    } catch (Exception e) {
	      throw new BuilderException("Error parsing SQL Mapper Configuration. Cause: " + e, e);
	    }
	  }
	}


Through these source code we know in conifg file:

1. configuration is the root node.

2. under configuration node, we can config 10 nodes:properties, typeAliases, plugins, objectFactory, objectWrapperFactory, settings, environments, databaseIdProvider, typeHandlers, mappers.