# Chapter 1


Some(xx)结构赋值时，不能直接move mut var的ownership

```rs
#[derive(PartialEq, Eq, Debug, Clone)]
pub struct ListNode {
    pub val: i32,
    pub next: Option<Box<ListNode>>,
}

impl ListNode {
    #[inline]
    fn new(val: i32) -> Self {
        ListNode {
            val: val,
            next: None,
        }
    }
}

struct Solution {}

impl Solution {
    fn new() -> Self {
        Solution {}
    }

    pub fn delete_node(&self, head: Option<Box<ListNode>>, val: i32) -> Option<Box<ListNode>> {
        let mut head_v = Box::new(ListNode::new(-1));
        head_v.next = head;
        let mut p = &mut head_v;
        //为啥要take()
        //如果直接写会报错，提示如下。我想大概是因为，p本身是mut的，不能将mut p中的一部分ownership 直接move 走。要用take()函数会更安全
        //while let Some(nt) = p.next {
        /*
        cannot move out of `p.next` as enum variant `Some` which is behind a mutable referencerustcClick for full compiler diagnostic
        main.rs(31, 24): data moved here
        main.rs(31, 24): move occurs because `nt` has type `Box<ListNode>`, which does not implement the `Copy` trait
        main.rs(31, 30): consider borrowing here: `&`
         */
        while let Some(nt) = p.next.take() {
            if nt.val == val {
                p.next = nt.next;
            } else {
                p.next = Some(nt);
                // p = &mut p.next.as_mut().unwrap();
                p = p.next.as_mut().unwrap();
            }
        }

        head_v.next
    }
}

fn main() {
    // Create the linked list 2 -> 5 -> 1 -> 9
    let mut head:Box<ListNode> = Box::new(ListNode::new(2));
    head.next = Some(Box::new(ListNode::new(5)));
    head.next.as_mut().unwrap().next = Some(Box::new(ListNode::new(1)));
    head.next.as_mut().unwrap().next.as_mut().unwrap().next = Some(Box::new(ListNode::new(9)));

    // Print the original linked list
    println!("Original Linked List: {:?}", head);

    // Create an instance of Solution
    let solution = Solution::new();

    // Call deleteNode method to delete a node with value 5
    let result = solution.delete_node(Some(head), 5);

    // Print the modified linked list
    println!("Modified Linked List: {:?}", result);

}

```