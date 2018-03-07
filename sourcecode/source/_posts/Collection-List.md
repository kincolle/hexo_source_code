---
title: List
date: 2018-03-06 01:01:21
tags:
categories:
- Java Learning
- Collection
- 2.List
---
# 2 List
List is an ordered collection and it allow duplicate elements. The user of this interface has precise control over where in the list each element is inserted. The user can access elements by their integer index (position in the list), and search for elements in the list.<br>
In List interface, there a four implementations: ArrayList, Vector, Stack and LinkedList.<br>
There are some functions in the List interface.

| Function   |      Description      |
|----------|:-------------:|
| boolean add(E e) |  add element, if success it will reture true |
| boolean addAll(Collection<? extends E> c) |    add one list into the current list, if success it will reture true   |
| boolean remove(Object o) | delete one element, if success it will reture true |
| E remove(int index) | delete one element at the index of the element, will ruturn the element previously at the specified position |
| boolean contains(Object o) | return true if contain o |
| public E set(int index, E element) | replace the element at the input index |
| public boolean isEmpty() | return true if the list is empty |
| public int indexOf(Object o) | Returns the index of the first occurrence of the specified element in this list, or -1 if this list does not contain the element. |
| public int lastIndexOf(Object o) | Returns the index of the last occurrence of the specified element in this list, or -1 if this list does not contain the element. |
| public List<E> subList(int fromIndex, int toIndex) | get a sublist with index that from fromIndex to toIndex |
| public Object[] toArray() | Returns an array containing all of the elements in this list in proper sequence |
| public void clear() | RRemoves all of the elements from this list. |

## 2.1 ArrayList
### 2.1.1 Introduction of ArrayList 
ArrayList is implemented by using array and the capacity of array can be change dinamically. When the used capacity of one define ArrayList is over 50%, it will use System.arraycopy() to copy to one new Array for creating capacity. Also, you can change the capacity before it is over 50% by using ensureCapacity. So, if you want to use it, you'd better forecast the space. The default space is 10. By the way, it's not thread safe. So, when the situation is multi-thread, you'd better use "Collections.synchronizedList(List l)" or use "CopyOnWriteArrayList" class. 

### 2.1.2 Implemetation of Interface

	public class ArrayList<E> extends AbstractList<E>
        implements List<E>, RandomAccess, Cloneable, java.io.Serializable
	{
	}

ArrayList extends AbstractList and implement List. It's a list of array and it has functions like add, remove, set, iterator and so on.<br>
ArrayList implement RandomAccess interface, so it can use random invocation. In ArrayList, we can use index to get object quikcly and that's random invocation.<br>
ArrayList implement Cloneable interface. which means it override clone(), so it can use clone.<br>
ArrayList implement java.io.Serializable interface, so it can be serialized and can translate data by using serialization.

### 2.1.3 Array of ArrayList
Here is the source code:

	/**
	* The array buffer into which the elements of the ArrayList are stored.
	* The capacity of the ArrayList is the length of this array buffer.
	*/
	private transient Object[] elementData;

We can see that ArrayList uses array to store data.

### 2.1.4 Construction
Here is the source code:

    /**
     * Constructs an empty list with an initial capacity of ten.
     */
    public ArrayList() {
        this(10);
    }
    /**
     * Constructs an empty list with the specified initial capacity.
     *
     * @param  initialCapacity  the initial capacity of the list
     * @throws IllegalArgumentException if the specified initial capacity
     *         is negative
     */
    public ArrayList(int initialCapacity) {
        super();
        if (initialCapacity < 0)
            throw new IllegalArgumentException("Illegal Capacity: "+
                                               initialCapacity);
        this.elementData = new Object[initialCapacity];
    }

    /**
     * Constructs a list containing the elements of the specified
     * collection, in the order they are returned by the collection's
     * iterator.
     *
     * @param c the collection whose elements are to be placed into this list
     * @throws NullPointerException if the specified collection is null
     */
    public ArrayList(Collection<? extends E> c) {
        elementData = c.toArray();
        size = elementData.length;
        // c.toArray might (incorrectly) not return Object[] (see 6260652)
        if (elementData.getClass() != Object[].class)
            elementData = Arrays.copyOf(elementData, size, Object[].class);
    }

ArrayList has three kinds of contructions:

- public ArrayList() can create a new ArrayList with capacity of 10.
- public ArrayList(int initialCapacity) can create a new ArrayList with capacity of the initialCapacity.
- public ArrayList(Collection<? extends E> c) can create a new ArrayList which contains the input collection.
 
### 2.1.5 Storage
In ArrayList there are some kinds of function that adding elements. I will show you in the latter.

##### set(int index, E element)
This function will invoca rangeCheck(index) firstly to check whether index is out of the range of the array and throw excaption if it's true. Then, get the location of the index and write the new element into it, and return the old element.<br>
Here is the source code:

    /**
     * Replaces the element at the specified position in this list with
     * the specified element.
     *
     * @param index index of the element to replace
     * @param element element to be stored at the specified position
     * @return the element previously at the specified position
     * @throws IndexOutOfBoundsException {@inheritDoc}
     */
    public E set(int index, E element) {
        rangeCheck(index);

        E oldValue = elementData(index);
        elementData[index] = element;
        return oldValue;
    }
    /**
      * Checks if the given index is in range.  If not, throws an appropriate
      * runtime exception.  This method does *not* check if the index is
      * negative: It is always used immediately prior to an array access,
      * which throws an ArrayIndexOutOfBoundsException if index is negative.
      */
      private void rangeCheck(int index) {
        if (index >= size)
        throw new IndexOutOfBoundsException(outOfBoundsMsg(index));
      }

##### add(E e)
This function will add new element into the tail of a arraylist, when the capacity is not enough it will use grow() to increase the capacity. Here is the source code:

    /**
     * Appends the specified element to the end of this list.
     *
     * @param e element to be appended to this list
     * @return <tt>true</tt> (as specified by {@link Collection#add})
     */
    public boolean add(E e) {
        ensureCapacityInternal(size + 1);  // Increments modCount!!
        elementData[size++] = e;
        return true;
    }
    private void ensureCapacityInternal(int minCapacity) {
        modCount++;
        // overflow-conscious code
        if (minCapacity - elementData.length > 0)
            grow(minCapacity);
    }
    private void grow(int minCapacity) {
        // overflow-conscious code
        int oldCapacity = elementData.length;
        int newCapacity = oldCapacity + (oldCapacity >> 1);
        if (newCapacity - minCapacity < 0)
            newCapacity = minCapacity;
        if (newCapacity - MAX_ARRAY_SIZE > 0)
            newCapacity = hugeCapacity(minCapacity);
        // minCapacity is usually close to size, so this is a win:
        elementData = Arrays.copyOf(elementData, newCapacity);
    }

##### add(int index, E element)
This function will insert a element at the index position. The source code is here:

    /**
     * Inserts the specified element at the specified position in this
     * list. Shifts the element currently at that position (if any) and
     * any subsequent elements to the right (adds one to their indices).
     *
     * @param index index at which the specified element is to be inserted
     * @param element element to be inserted
     * @throws IndexOutOfBoundsException {@inheritDoc}
     */
    public void add(int index, E element) {
        rangeCheckForAdd(index);

        ensureCapacityInternal(size + 1);  // Increments modCount!!
        System.arraycopy(elementData, index, elementData, index + 1,
                         size - index);
        elementData[index] = element;
        size++;
    }

##### addAll(Collection<? extends E> c) & addAll(int index, Collection<? extends E> c)
This function will insert a whole collection into tail of a arraylist or at the index position. Here is the source code:

/**
     * Appends all of the elements in the specified collection to the end of
     * this list, in the order that they are returned by the
     * specified collection's Iterator.  The behavior of this operation is
     * undefined if the specified collection is modified while the operation
     * is in progress.  (This implies that the behavior of this call is
     * undefined if the specified collection is this list, and this
     * list is nonempty.)
     *
     * @param c collection containing elements to be added to this list
     * @return <tt>true</tt> if this list changed as a result of the call
     * @throws NullPointerException if the specified collection is null
     */
    public boolean addAll(Collection<? extends E> c) {
        Object[] a = c.toArray();
        int numNew = a.length;
        ensureCapacityInternal(size + numNew);  // Increments modCount
        System.arraycopy(a, 0, elementData, size, numNew);
        size += numNew;
        return numNew != 0;
    }

    /**
     * Inserts all of the elements in the specified collection into this
     * list, starting at the specified position.  Shifts the element
     * currently at that position (if any) and any subsequent elements to
     * the right (increases their indices).  The new elements will appear
     * in the list in the order that they are returned by the
     * specified collection's iterator.
     *
     * @param index index at which to insert the first element from the
     *              specified collection
     * @param c collection containing elements to be added to this list
     * @return <tt>true</tt> if this list changed as a result of the call
     * @throws IndexOutOfBoundsException {@inheritDoc}
     * @throws NullPointerException if the specified collection is null
     */
    public boolean addAll(int index, Collection<? extends E> c) {
        rangeCheckForAdd(index);

        Object[] a = c.toArray();
        int numNew = a.length;
        ensureCapacityInternal(size + numNew);  // Increments modCount

        int numMoved = size - index;
        if (numMoved > 0)
            System.arraycopy(elementData, index, elementData, index + numNew,
                             numMoved);

        System.arraycopy(a, 0, elementData, index, numNew);
        size += numNew;
        return numNew != 0;
    }

### 2.1.6 Get
This function can use random access because of using array. When invoke get(), it will check the range of index and will return a element if it's not out of the range. Here is the source code:

	/**
	* Returns the element at the specified position in this list.
	*
	* @param  index index of the element to return
	* @return the element at the specified position in this list
	* @throws IndexOutOfBoundsException {@inheritDoc}
	*/
	public E get(int index) {
	    rangeCheck(index);
    	return (E) elementData[index];
	}
	private void rangeCheck(int index) {
	    if (index >= size)
	    throw new IndexOutOfBoundsException(outOfBoundsMsg(index));
	}  

### 2.1.7 Remove
You can remove a element by using an index or an object. When you remove one element successfully, the elements after the removed element will go left by one index. Here is the source code:

    /**
     * Removes the element at the specified position in this list.
     * Shifts any subsequent elements to the left (subtracts one from their
     * indices).
     *
     * @param index the index of the element to be removed
     * @return the element that was removed from the list
     * @throws IndexOutOfBoundsException {@inheritDoc}
     */
    public E remove(int index) {
        rangeCheck(index);

        modCount++;
        E oldValue = elementData(index);

        int numMoved = size - index - 1;
        if (numMoved > 0)
            System.arraycopy(elementData, index+1, elementData, index,
                             numMoved);
        elementData[--size] = null; // Let gc do its work

        return oldValue;
    }
	/**
     * Removes the first occurrence of the specified element from this list,
     * if it is present.  If the list does not contain the element, it is
     * unchanged.  More formally, removes the element with the lowest index
     * <tt>i</tt> such that
     * <tt>(o==null&nbsp;?&nbsp;get(i)==null&nbsp;:&nbsp;o.equals(get(i)))</tt>
     * (if such an element exists).  Returns <tt>true</tt> if this list
     * contained the specified element (or equivalently, if this list
     * changed as a result of the call).
     *
     * @param o element to be removed from this list, if present
     * @return <tt>true</tt> if this list contained the specified element
     */
    public boolean remove(Object o) {
        if (o == null) {
            for (int index = 0; index < size; index++)
                if (elementData[index] == null) {
                    fastRemove(index);
                    return true;
                }
        } else {
            for (int index = 0; index < size; index++)
                if (o.equals(elementData[index])) {
                    fastRemove(index);
                    return true;
                }
        }
        return false;
    }

### 2.1.8 Change the Capacity of Array
When I describe storage of ArrayList I have said that when add new element it will check the capacity of the array, and it will increase the capacity if over the capacity. There are two functions can increase the capacity: (1) developer can use "ensureCapacity(int minCapacity)" to increase the capacity (2) if the capacity is not enough when adding the "private void ensureCapacityInternal(int minCapacity)" will be invoked. Here is the source code:

    public void ensureCapacity(int minCapacity) {
        if (minCapacity > 0)
            ensureCapacityInternal(minCapacity);
    }

    private void ensureCapacityInternal(int minCapacity) {
        modCount++;
        // overflow-conscious code
        if (minCapacity - elementData.length > 0)
            grow(minCapacity);
    }
    /**
     * Increases the capacity to ensure that it can hold at least the
     * number of elements specified by the minimum capacity argument.
     *
     * @param minCapacity the desired minimum capacity
     */
    private void grow(int minCapacity) {
        // overflow-conscious code
        int oldCapacity = elementData.length;
        int newCapacity = oldCapacity + (oldCapacity >> 1);
        if (newCapacity - minCapacity < 0)
            newCapacity = minCapacity;
        if (newCapacity - MAX_ARRAY_SIZE > 0)
            newCapacity = hugeCapacity(minCapacity);
        // minCapacity is usually close to size, so this is a win:
        elementData = Arrays.copyOf(elementData, newCapacity);
    }

From the source code we can see that when increase the capacity old arraylist will be copied into one new arraylist. The capacity of the new arraylist is always 1.5 times of the old one according to the source code "int newCapacity = oldCapacity + (oldCapacity >> 1);". The price of this execution is high, so we'd better avoid it.

### 2.1.9 Fail-Fast
ArrayList also use Fail-Fast function by using modCount. The detail description of Fail-Fast is introduced in HashMap Chapter. 

## 2.2 LinkedList
### 2.2.1 Introduction
Like ArrayList, LinkedList implements List too, but the inner data structure is different. LinkedList is constructed by using linked list.  Here is the defined source code:

	public class LinkedList<E>
	    extends AbstractSequentialList<E>
	    implements List<E>, Deque<E>, Cloneable, java.io.Serializable
	{}

As we can see that LinkedList extends AbstractSequentialList and implements List, Deque, Cloneable, java.io.Serializable.<br>
LinkedList is using Fail-Fast too.

### 2.2.2 Data Structure
A linked list is represented by a pointer to the first node of the linked list and the first node is called head. If the linked list is empty, then value of head is NULL. Each node in a list consists of at least two parts: 1) data 2) pointer to the next node. Here is the source code:

    transient int size = 0;
    transient Node<E> first; //链表的头指针
    transient Node<E> last; //尾指针
    //存储对象的结构 Node, LinkedList的内部类
    private static class Node<E> {
        E item;
        Node<E> next; // 指向下一个节点
        Node<E> prev; //指向上一个节点

        Node(Node<E> prev, E element, Node<E> next) {
            this.item = element;
            this.next = next;
            this.prev = prev;
        }
    }

### 2.2.4 Construction
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



### 2.2.5 Storage
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

