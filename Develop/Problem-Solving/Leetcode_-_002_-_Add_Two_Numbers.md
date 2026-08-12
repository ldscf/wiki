---
source_title: Leetcode - 002 - Add Two Numbers
categories:
- Develop
- Problem-Solving
last_modified: '2024-05-17T06:22:31Z'
---
2. Add Two Numbers
- Medium

You are given two non-empty linked lists representing two non-negative integers. The digits are stored in reverse order, and each of their nodes contains a single digit. Add the two numbers and return the sum as a linked list.

You may assume the two numbers do not contain any leading zero, except the number 0 itself. 
```
 *Example 1:
 Input: l1 = [2,4,3], l2 = [5,6,4]
 Output: [7,0,8]
 Explanation: 342 + 465 = 807.
```

 
```
 Example 2:
 Input: l1 = [0], l2 = [0]
 Output: [0]
```

 
```
 Example 3:
 Input: l1 = [9,9,9,9,9,9,9], l2 = [9,9,9,9]
 Output: [8,9,9,9,0,0,0,1]
```


```
 Constraints:
```

     The number of nodes in each linked list is in the range [1, 100].

     0 <= Node.val <= 9

     It is guaranteed that the list represents a number that does not have leading zeros.*

Accepted 4.6M, Submissions 10.6M, Acceptance Rate 42.9%

### Solution

# Create a ListNode to store the additive results and an integer variable i to store the carry

# Traverse the ListNodes, additive for each node

# If the result of the node > 10, a number greater than a single digit is put into i

# When the ListNode to be added is empty (the accumulation is complete) and the i = 0 (no carry), return the result ListNode

#### Java
```
 *import java.util.Arrays;
 import java.util.List;
```

 
```
 class ListNode {
```

     public int val;

     public ListNode next;

     public ListNode() {}

     public ListNode(int val) { this.val = val; }

     public ListNode(int val, ListNode next) { this.val = val; this.next = next; }
```
 }
```

 
```
 public class lc002 {
```

     public static ListNode addTwoNumbers(ListNode ln1, ListNode ln2) {

         ListNode ln0 = new ListNode(0);

         ListNode lnres = ln0;

         ListNode lntmp;
 
         int i = 0;

         while (true) {

             lntmp = ln1;

             if (lntmp !=null) {

                 ln0.val += lntmp.val;

                 lntmp = lntmp.next;

             }

             ln1 = lntmp;
 
             lntmp = ln2;

             if (lntmp !=null) {

                 ln0.val += lntmp.val;

                 lntmp = lntmp.next;

             }

             ln2 = lntmp;
 
             while (ln0.val > 9) {

                 ln0.val -= 10;

                 i++;

             }
 
             if (ln1 != null || ln2 != null || i>0) {

                 ln0.next = new ListNode(0);

                 ln0 = ln0.next;

                 ln0.val = i;

                 i = 0;

             } else {

                 break;

             }

         }
 
         return lnres;

     }
 
     public static ListNode ListNodeInit(List<Integer> l1) {

         ListNode lnres, lntmp;

         lntmp = new ListNode(l1.get(0));

         lnres = lntmp;

         for (int i=1; i<l1.size(); i++) {

             lntmp.next = new ListNode(l1.get(i));

             lntmp = lntmp.next;

         }

         return lnres;

     }
 
     public static void ListNodePrint(ListNode ln1) {

         System.out.println("=== ListNode Print ===");

         if (ln1 == null) {System.out.println("[]");return;}

         System.out.print("[");

         while(true) {

             System.out.print(String.format("%s", ln1.val));

             ln1 = ln1.next;

             if (ln1 == null)

                 break;

             else

                 System.out.print(",");

         }

         System.out.println("]");

     }
 
     public static void main(String[] args) {

         ListNode ln1, ln2;

         List<Integer> l1, l2;
 
         //sample1

         l1 = Arrays.asList(2,3,4);

         l2 = Arrays.asList(5,6,4);
```
 //
 //        //sample2
 //        l1 = Arrays.asList(0);
 //        l2 = Arrays.asList(0);
 //
 //        //sample3
 //        l1 = Arrays.asList(9,9,9,9,9,9,9);
 //        l2 = Arrays.asList(9,9,9,9);
```

 
         System.out.println(l1);

         System.out.println(l2);
 
         ln1 = ListNodeInit(l1);

         ln2 = ListNodeInit(l2);
 
         ListNode lnres = addTwoNumbers(ln1, ln2);

         ListNodePrint(lnres);
 
         ListNodePrint(addTwoNumbers(ln1, null));

         ListNodePrint(addTwoNumbers(null, ln2));

         ListNodePrint(addTwoNumbers(null, null));

         ListNodePrint(null);

     }
```
 }*
```
