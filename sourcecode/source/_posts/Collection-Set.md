---
title: Set
date: 2018-03-06 01:01:46
tags:
categories:
- Java Learning
- Collection
- 5.Set
---
# Set
## 1 Introduction of HashSet
HashSet is implemented by using HashMap. So, if you know HashMao very well, you can understand HashSet quickly.<br>
Here let's see the construction of HashSet:

    private transient HashMap<E,Object> map;

    // Dummy value to associate with an Object in the backing Map
    private static final Object PRESENT = new Object();

    /**
     * Constructs a new, empty set; the backing <tt>HashMap</tt> instance has
     * default initial capacity (16) and load factor (0.75).
     */
    public HashSet() {
        map = new HashMap<>();
    }

We can see that it is constructed by using HashMap.

## 2 Implementation of HashSet
For HashSet, since it uses HashMap, the element is stored in HashMap．So most function of HashSet is the same to HashMap. 
##### construction
	 /**
	 * defaulf constructor and will construct a empty HashSet
	 *
	 * In fact it will construct a empty HashMap with Capacity of 16 and loadFactor of 0.75
	 */
	public HashSet() {
	    map = new HashMap<E,Object>();
	}
	
	/**
	 * Create a new HashSet with all elements of the collection
	 *
	 * it will construct a empty HashMap with Capacity of 16 and loadFactor of 0.75
	 * @param element of c will be stored in this HashSet
	 */
	public HashSet(Collection<? extends E> c) {
	    map = new HashMap<E,Object>(Math.max((int) (c.size()/.75f) + 1, 16));
	    addAll(c);
	}
	
	/**
	 * create a new empty HashSet with Capacity of initialCapacity and loadFactor of loadFactor
	 *
	 * @param initialCapacity 
	 * @param loadFactor 
	 */
	public HashSet(int initialCapacity, float loadFactor) {
	    map = new HashMap<E,Object>(initialCapacity, loadFactor);
	}
	
	/**
	 * build a new empty HashSet with Capacity of initialCapacity and loadFactor of 0.75
	 *
	 * @param initialCapacity 
	 */
	public HashSet(int initialCapacity) {
	    map = new HashMap<E,Object>(initialCapacity);
	}
	
##### add
Here is the source code:

	/**
	
	 * @param e the e will be put into the set
	 * @return if there is no e in this set, then returen ture
	 */
	public boolean add(E e) {
	    return map.put(e, PRESENT)==null;
	}

If the set is not containing e, then it will be put into the set. <br>
In HashMap, will put key-value, the key will never be duplicated. That's why the elements of set will never be duplicated.