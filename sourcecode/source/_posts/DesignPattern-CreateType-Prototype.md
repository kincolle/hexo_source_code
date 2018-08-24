---
title: Prototype
date: 2018-05-16 10:12:52
tags:
categories:
- Design Pattern
- CreateType
- Prototype
---
## Prototype
The prototype pattern is a creational design pattern in software development. It is used when the type of objects to create is determined by a prototypical instance, which is cloned to produce new objects. 

Here is the java code example:

	public class Prototype implements Cloneable, Serializable {
	
	    private static final long serialVersionUID = 1L;
	    private String string;
	
	    private SerializableObject obj;
	
	    /* clone */
	    public Object clone() throws CloneNotSupportedException {
	        Prototype proto = (Prototype) super.clone();
	        return proto;
	    }
	
	    /* deepClone */
	    public Object deepClone() throws IOException, ClassNotFoundException {
	
	        /* Write 2 byte stream of this object */
	        ByteArrayOutputStream bos = new ByteArrayOutputStream();
	        ObjectOutputStream oos = new ObjectOutputStream(bos);
	        oos.writeObject(this);
	
	        /* Read 2 byte stream of this object */
	        ByteArrayInputStream bis = new ByteArrayInputStream(bos.toByteArray());
	        ObjectInputStream ois = new ObjectInputStream(bis);
	        return ois.readObject();
	    }
	
	    public String getString() {
	        return string;
	    }
	
	    public void setString(String string) {
	        this.string = string;
	    }
	
	    public SerializableObject getObj() {
	        return obj;
	    }
	
	    public void setObj(SerializableObject obj) {
	        this.obj = obj;
	    }
	
	}
	
	class SerializableObject implements Serializable {
	    private static final long serialVersionUID = 1L;
	}