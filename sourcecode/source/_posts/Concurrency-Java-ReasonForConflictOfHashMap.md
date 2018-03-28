---
title: ReasonForConflictOfHashMap
date: 2018-03-22 23:59:02
tags:
categories:
- Java Learning
- Concurrency
- 9.ReasonForConflictOfHashMap
---
## Put and Get of HashMap
I have introduced put and get of HashMap at [here](https://kincolle.github.io/2018/03/06/Collection-HashMapAndHashSet/). To introduce the reason for conflict of HashMap, I'd like to introduce put and get of HashMap again.<br>
Here, let's see how put work:

	public V put(K key, V value) {
        if (key == null)
            return putForNullKey(value);
        // get hash value for key
    	int hash = hash(key);
        // get the index of array
        int i = indexFor(hash, table.length);
        // if at i position is not null, the using next to iterate the next element
        for (Entry<K,V> e = table[i]; e != null; e = e.next) {
            Object k;
            if (e.hash == hash && ((k = e.key) == key || key.equals(k))) {
                V oldValue = e.value;
                e.value = value;
                e.recordAccess(this);
                return oldValue;
            }
        }
 
        modCount++;
        addEntry(hash, key, value, i);
        return null;
    }

Here use some functions:

	static int hash(int h) {
        h ^= (h >>> 20) ^ (h >>> 12);
        return h ^ (h >>> 7) ^ (h >>> 4);
    }
     
	static int indexFor(int h, int length) {
        return h & (length-1);
    }

When we finished put(), here is how we use get()

	public V get(Object key) {
        if (key == null)
            return getForNullKey();
        int hash = hash(key.hashCode());
        /**
          * first find the position in the array
          * then check whether the hashcode of key equals and the key value equals
          */
        for (Entry<K,V> e = table[indexFor(hash, table.length)];e != null;e = e.next) {
            Object k;
            if (e.hash == hash && ((k = e.key) == key || key.equals(k)))
                return e.value;
        }
        return null;
	}

Check the constructure of the Entry:

	static class Entry<K,V> implements Map.Entry<K,V> {
       final K key;//key of Key-value
       V value;// stored value
       Entry<K,V> next;// point to next entry
       final int hash;// hashcode
 	}

## Rehash
I have introduced rehash of HashMap in [here](https://kincolle.github.io/2018/03/06/Collection-HashMapAndHashSet/). I plan to introduce it in detail.<br>
At the best situation, the runtime of both get and put is O(1). But in fact, there has conflict will more than one entry at the same index of array. So the linked list is used for solving this problem, but the runtime will be O(N). So, to let the hash be meaningful, we must keep a balance of size of array and linkedlist. That is why we need to rehash.<br>
Here let's see the addEntry():

	void addEntry(int hash, K key, V value, int bucketIndex) {
		Entry<K,V> e = table[bucketIndex];
        table[bucketIndex] = new Entry<K,V>(hash, key, value, e);
        if (size++ >= threshold)
			resize(2 * table.length);
	}

Here is the resize that change the size of table and make it double:

	void resize(int newCapacity) {
       Entry[] oldTable = table;
       int oldCapacity = oldTable.length;
       if (oldCapacity == MAXIMUM_CAPACITY) {
           threshold = Integer.MAX_VALUE;
           return;
       }
 
       Entry[] newTable = new Entry[newCapacity];
       //initial a new table that size is twice than old table
       transfer(newTable);
       table = newTable;
       threshold = (int)(newCapacity * loadFactor);
	}

The point is transfer(newTable), this operation wiill move the current elements of Entry[] table into the new table. At multi-threads situation, problems may happen which is Infinite Loop.  

## Infinite Loop Problem

##### 1. Persuming we have thread T1 and thread T2.
Here let's see the transfer:

	oid transfer(Entry[] newTable) {
        Entry[] src = table;
        int newCapacity = newTable.length;
        for (int j = 0; j < src.length; j++) {
            Entry<K,V> e = src[j];
            if (e != null) {
                src[j] = null;
                do {
        // persuming the first thread T1 is hung out for some reason at here  
                    Entry<K,V> next = e.next;
                    int i = indexFor(e.hash, newCapacity);
                    e.next = newTable[i];
                    newTable[i] = e;
                    e = next;
                } while (e != null);
            }
        }
    }
 
Here let's see how the problem happen:
Persuming thread T2 finished and will be like the following:

![](/Concurrency-Java-ReasonForConflictOfHashMap/HashMap02.jpg)

Here,because e of T1 points to key(3) and its next points to key(7), and after T2 rehashed, the order are reversed.

##### 2. T1 do work

- T1 do "newTalbe[i] = e;"
- then do "e = next", that leads e point to key(7)
- At the next loop, "next = e.next" let next point to key(3)

![](/Concurrency-Java-ReasonForConflictOfHashMap/HashMap03.jpg)

##### 3. Continue
T1 continue to work. Put key(7)down and set it at the first place of newTable[i],  then move e and next to the next one. 

![](/Concurrency-Java-ReasonForConflictOfHashMap/HashMap04.jpg)

##### 4. Infinite Loop
The e.next = newTable[i] let key(3).next point to key(7). But, at this time, key(7).next has already pointed to key(3), so the infinite loop happens. 

![](/Concurrency-Java-ReasonForConflictOfHashMap/HashMap05.jpg)

Then, when T1 invokes HashTable.get(11), inifinite loop happens.