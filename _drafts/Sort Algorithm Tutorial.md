---
layout: post
title: Sort Algorithm Tutorial
author: Andy Feng
---

# Introduction
Sorting algorithm is an algorithm that put elements of a list into an order, either ascending or descending order.

# Bubble Sorting
bubble sorting a simple algorithm which repeatly swapping adjacent elements if they are in wrong order. The algorithm is not suitable for large data sets as its average and worse time complexity is high.

e.g.

	public static int[] Sort(int[] numbers)
	{
	    if (numbers == null || numbers.Length == 0) return new int[] { };
	    for (int i = 0; i < numbers.Length - 1; i++)
	    {
	        for (int j = 0; j < numbers.Length-1-i; j++)
	        {
	            if (numbers[j] > numbers[j+1])
	            {
	                // swap
	                var temp = numbers[j];
	                numbers[j] = numbers[j+1];
	                numbers[j + 1] = temp;
	            }
	        }
	    }
	    return numbers;
	}

each iteration, the last element was put at the end.

# Merge algorithm
Merge sort is a divide and conquer algorithm. It keep splitting an array into two halves sub arrays and recursively sort each sub array. Then merge them back into a complete sorted array.

Pseudocode:

mergeSort(array)
	if (array.length <=1) themn return array;
	mid = array.length / 2;
	left = new array();
	right = new array();
	mergeSort(left);
	mergeSort(right);
	merge(left, right);

merge(left, right)
	result = new array(left.length + right.length);
	loop left and right
	add element to result

# Quick algorithm
Qyuck algorithm is also divide and conquer algorithm like Merge algorithm. It first select a pivot(reference element), then keep partitioning the list. Use recursive strategy to sort each partitioned array. In each partition, loop from beginning and end to the middle.

By default, the pivot is selected as the first element in the list.

Pseudocode:

quickSort(array, left, right)
if (left < right) {
	pivotIndex = partition(array, left, end)
	quickSort(array, left, pivotIndex);
	quickSort(array, pivotIndex, right); 
}

partition(array, left, right){
	pivotIndex = left;
	for(int i = pivotIndex+1; i < right; i++){
		if (array[i] < array[pivotIndex] 
			swap array[i] array[pivotIndex+1]
	}
}
# Reference
[冒泡排序](https://www.runoob.com/w3cnote/bubble-sort.html)

[Merge Sort In C#](https://www.c-sharpcorner.com/blogs/a-simple-merge-sort-implementation-c-sharp)

[Quicksort Algorithm in C#](https://code-maze.com/csharp-quicksort-algorithm/)