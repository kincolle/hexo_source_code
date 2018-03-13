---
title: LinkedList
date: 2018-03-13 08:45:51
tags:
categories:
- Java Learning
- Collection
- LinkedList
---
# LinkedList
## 1 Introduction
Like ArrayList, LinkedList implements List too, but the inner data structure is different. LinkedList is constructed by using linked list.  Here is the defined source code:

	public class LinkedList<E>
	    extends AbstractSequentialList<E>
	    implements List<E>, Deque<E>, Cloneable, java.io.Serializable
	{}

As we can see that LinkedList extends AbstractSequentialList and implements List, Deque, Cloneable, java.io.Serializable.<br>
LinkedList is using Fail-Fast too.

## 2 Data Structure
A linked list is represented by a pointer to the first node of the linked list and the first node is called head. If the linked list is empty, then value of head is NULL. Each node in a list consists of at least two parts: 1) data 2) pointer to the next node. Here is the source code:

    transient int size = 0;
    transient Node<E> first; // head pointer of linkedlist
    transient Node<E> last; // tail pointer
    // storage object node
    private static class Node<E> {
        E item;
        Node<E> next; // point to next node
        Node<E> prev; // point to last node

        Node(Node<E> prev, E element, Node<E> next) {
            this.item = element;
            this.next = next;
            this.prev = prev;
        }
    }

## 3 Construction
There are two construct functions: LinkedLis()和LinkedList(Collection<? extends E> c):

	/**
     *  Create a empty LinkedList
     */
    public LinkedList() {
        header.next = header.previous = header;
    }
 
    /**
     *  Create a empty LinkedList with elements of the input collection 
     */
    public LinkedList(Collection<? extends E> c) {
        this();
        addAll(c);
    }



## 4 Storage
##### add(E e)
This function will add a new element at the tail of one LinkedList. First of all, this function will let the last node point to a new Node(l), and according to l create a newNode and its element the input one. Then we let the last node point to newNode. Here is the source code:

	/**
	     * Appends the specified element to the end of this list.
	     *
	     * <p>This method is equivalent to {@link #addLast}.
	     *
	     * @param e element to be appended to this list
	     * @return {@code true} (as specified by {@link Collection#add})
	     */
	public boolean add(E e) {
	    linkLast(e);
	    return true;
	}
	/**
	* Links e as last element.
	*/
	void linkLast(E e) {
	    final Node<E> l = last;
	    final Node<E> newNode = new Node<>(l, e, null);
	    last = newNode;
	    if (l == null)
	        first = newNode;
	    else
	        l.next = newNode;
	    size++;
	    modCount++;
	}

##### add(int index, E element)
This function let you can insert one element at the index position. If the index equals size, the inserted position will be the tail of linkedlist. Here is the source code:

	/**
    * Inserts the specified element at the specified position in this list.
    * Shifts the element currently at that position (if any) and any
    * subsequent elements to the right (adds one to their indices).
    *
    * @param index index at which the specified element is to be inserted
    * @param element element to be inserted
    * @throws IndexOutOfBoundsException {@inheritDoc}
    */
	public void add(int index, E element) {
       checkPositionIndex(index);

       if (index == size)
           linkLast(element);
       else
           linkBefore(element, node(index));
	}
	/**
        * Inserts element e before non-null Node succ.
        */
    void linkBefore(E e, Node<E> succ) {
       // assert succ != null;
       final Node<E> pred = succ.prev
       final Node<E> newNode = new Node<>(pred, e, succ);
       succ.prev = newNode;
       if (pred == null)
           first = newNode;
       else
           pred.next = newNode;
       size++;
       modCount++;
    }

#####  addAll(Collection<? extends E> c) and addAll(int index, Collection<? extends E> c)
addAll will add all elements of input collection at the tail of linkedlist or at the position of index. Here is the source code:

	
    public boolean addAll(Collection<? extends E> c) {
        return addAll(size, c);
    }
 
   
    public boolean addAll(int index, Collection<? extends E> c) {
        if (index < 0 || index > size)
            throw new IndexOutOfBoundsException("Index: " + index + ", Size: " + size);
        Object[] a = c.toArray();
        int numNew = a.length;    
       
        if (numNew == 0)
            return false;
        
        modCount++;
        
        Entry<E> successor = (index == size ? header : entry(index));
        Entry<E> predecessor = successor.previous;
        
        for (int i = 0; i < numNew; i++) {
            Entry<E> e = new Entry<E>((E) a[i], successor, predecessor);
            predecessor.next = e;
            predecessor = e;
        }
        successor.previous = predecessor;
        
        size += numNew;
        return true;
    }

