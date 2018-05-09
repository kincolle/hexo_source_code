---
title: Radix Sort
date: 2018-04-27 09:23:17
tags:
categories:
- Algorithm
- Sort
- Radix Sort
---
## Introduction
I copy an introduction of it from [wiki](https://en.wikipedia.org/wiki/Radix_sort)

>In computer science, radix sort is a non-comparative integer sorting algorithm that sorts data with integer keys by grouping keys by the individual digits which share the same significant position and value. A positional notation is required, but because integers can represent strings of characters (e.g., names or dates) and specially formatted floating point numbers, radix sort is not limited to integers. Radix sort dates back as far as 1887 to the work of Herman Hollerith on tabulating machines.

The following example will show you how it works:

ones place => | tens place => | hundreds place 
- | - | - 
53**1** | 3**1**2 | **1**23 
31**2** | 1**2**3 | **2**45
12**3** | 5**3**1 | **3**12
24**5** | 2**4**5 | **5**31

## Code
Here is an example code:

	public class RadixSorter {
	    public static boolean USE_LINK = true;
	
	    /**
	     *
	     * @param keys
	     * @param from
	     * @param len
	     * @param radix
	     * @param d
	     *            how many sub keys should one key divide to
	     */
	    public void sort(int[] keys, int from, int len, int radix, int d) {
	        if (USE_LINK) {
	            linkRadixSort(keys, from, len, radix, d);
	        } else {
	            arrayRadixSort(keys, from, len, radix, d);
	        }
	    }
	
	    private final void arrayRadixSort(int[] keys, int from, int len,
	                                      int radix, int d) {
	        int[] temporary = new int[len];
	        int[] count = new int[radix];
	        int R = 1;
	
	        for (int i = 0; i < d; i++) {
	            System.arraycopy(keys, from, temporary, 0, len);
	            Arrays.fill(count, 0);
	            for (int k = 0; k < len; k++) {
	                int subkey = (temporary[k] / R) % radix;
	                count[subkey]++;
	            }
	            
	            for (int j = 1; j < radix; j++) {
	                count[j] += count[j - 1];
	            }
	
	            for (int m = len - 1; m >= 0; m--) {
	                int subkey = (temporary[m] / R) % radix;
	                --count[subkey];
	                keys[from + count[subkey]] = temporary[m];
	            }
	            R *= radix;
	        }
	    }
	
	    private static class LinkQueue {
	        int head = -1;
	        int tail = -1;
	    }
	
	    private final void linkRadixSort(int[] keys, int from, int len,
	                                     int radix, int d) {
	        int[] nexts = new int[len];
	
	        LinkQueue[] queues = new LinkQueue[radix];
	        for (int i = 0; i < radix; i++) {
	            queues[i] = new LinkQueue();
	        }
	
	        for (int i = 0; i < len - 1; i++) {
	            nexts[i] = i + 1;
	        }
	        nexts[len - 1] = -1;
	
	        int first = 0;
	        for (int i = 0; i < d; i++) {
	            linkRadixSortDistribute(keys, from, len, radix, i, nexts, queues, first);
	            first = linkRadixSortCollect(keys, from, len, radix, i, nexts, queues);
	        }
	
	        int[] tmps = new int[len];
	        int k = 0;
	        while (first != -1) {
	
	            tmps[k++] = keys[from + first];
	            first = nexts[first];
	        }
	        System.arraycopy(tmps, 0, keys, from, len);
	    }
	
	    private final void linkRadixSortDistribute(int[] keys, int from, int len,
	                                               int radix, int d, int[] nexts, LinkQueue[] queues, int first) {
	
	        for (int i = 0; i < radix; i++) {
	            queues[i].head = queues[i].tail = -1;
	        }
	
	        while (first != -1) {
	            int val = keys[from + first];
	            for (int j = 0; j < d; j++) {
	                val /= radix;
	            }
	            val = val % radix;
	
	            if (queues[val].head == -1) {
	                queues[val].head = first;
	            } else {
	                nexts[queues[val].tail] = first;
	            }
	
	            queues[val].tail = first;
	            first = nexts[first];
	        }
	    }
	
	    private int linkRadixSortCollect(int[] keys, int from, int len, int radix,
	                                     int d, int[] nexts, LinkQueue[] queues) {
	        int first = 0;
	        int last = 0;
	        int fromQueue = 0;
	
	        for (; (fromQueue < radix - 1) && (queues[fromQueue].head == -1);
	             fromQueue++) {
	            ;
	        }
	
	        first = queues[fromQueue].head;
	        last = queues[fromQueue].tail;
	
	        while (fromQueue < radix - 1 && queues[fromQueue].head != -1) {
	            fromQueue += 1;
	            for (; (fromQueue < radix - 1) && (queues[fromQueue].head == -1);
	                 fromQueue++) {
	                ;
	            }
	
	            nexts[last] = queues[fromQueue].head;
	            last = queues[fromQueue].tail;
	        }
	        if (last != -1) {
	            nexts[last] = -1;
	        }
	        return first;
	    }
	
	    /**
	     * @param args
	     */
	    public static void main(String[] args) {
	      	int[] a = { 1, 9, 83, 34, 2,59, 54, 0, 77, 64, 95, 10, 9, 135, 14, 25, 121,
                12345, 9876, 11 };
	        USE_LINK = false;
	        RadixSorter sorter = new RadixSorter();
	        sorter.sort(a, 0, a.length, 10, 10);
	        System.out.println(Arrays.toString(a));
	    }
	}

The result will be:
	
	[0, 1, 2, 9, 9, 10, 11, 14, 25, 34, 54, 59, 64, 77, 83, 95, 121, 135, 9876, 12345]