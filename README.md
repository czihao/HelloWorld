# HelloWorld
\\@author zihao chen 
package avl;

import java.util.LinkedList;

import avl.util.TreeToStrings;

public class AVLTree<T extends Comparable<T>> {

	private TreeNode<T> root;
	public int size;
	
	public AVLTree() {
	    this.root = null;
	    this.size = 0;
	}
	
	////////////////////////////////////////////////////////
	
	//
	// exists()
	// Check whether a specified value exists in the set
	//
	public boolean exists(T value) {
	    return existsHelper(value, this.root);
	}
	
	//
	// existsHelper()
	// (Optionally) recursive procedure to traverse a tree
	// rooted at "root" to find a specified value.  
	//
	// RETURNS: true if the value is found, else false
	//
	private boolean existsHelper(T value, TreeNode<T> root) {
		if (root == null) { // not found
			return false;
	    } else {
	    	int comparison = root.value.compareTo(value);
		
	    	if (comparison == 0) { // found
	    		return true;
	    	} else if (comparison < 0) { // still looking - go left
	    		return existsHelper(value, root.left);
	    	} else { // still looking - go right
	    		return existsHelper(value, root.right);
	    	}
	    }
	}
	
	////////////////////////////////////////////////////////
	
	//
	// min()
	// Return the minimum value in the set
	//
	// If the set is empty, result is undefined.
	//
	public T min() {
	    return minValueInSubtree(this.root);
	}
	
	//
	// minValueInSubTree()
	// Find the smallest value in the subtree rooted at
	// the specified node.
	//
	// ASSUMED: root is not null.
	//
	private T minValueInSubtree(TreeNode<T> root) {
	    while (root.left != null)
	    	root = root.left;
	    
	    return root.value;
	}

	//
	// max()
	// Return the maximum value in the set
	//
	// If the set is empty, result is undefined.
	//
	
	public T max() {
	    return maxValueInSubtree(this.root);
	}


	//
	// maxValueInSubTree()
	// Find the largest value in the subtree rooted at
	// the specified node.
	//
	// ASSUMED: root is not null.
	//
	private T maxValueInSubtree(TreeNode<T> root) {
	    while (root.right != null)
	    	root = root.right;
	    
	    return root.value;
	}
	
	////////////////////////////////////////////////////////
	
	//
	// insert()
	// Insert the specified value in the set if it does not
	// already exist.
	//
	// RETURNS: the size of the set after insertion.
	//
	public int insert(T value) 
	{
	    this.root = insertHelper(value, this.root);
	    return size;
	}
	
	//
	// insertHelper()
	// Recursive procedure to insert a value into the subtree
	// rooted at "root".  If value is already present in the
	// tree, nothing is inserted.
	//
	// RETURNS: root node of subtree after insertion
	//
	// FIXME: add the necessary code to this function
	// to maintain height and rebalance the tree when
	// a node is removed.
	//
	private TreeNode<T> insertHelper(T value,
					 TreeNode<T> root) {
		if (root == null) {
			// add new element as leaf of tree
			TreeNode<T> newNode = new TreeNode<T>(value);
			newNode.height = 0;
			size++;
			return newNode;
	    } else {
	    	int comparison = value.compareTo(root.value);
		
	    	if (comparison == 0) {
	    		// duplicate element -- return existing node
	    		return root;
	    	} else if (comparison < 0) {
	    		// still looking -- go left
	    		root.setLeft(insertHelper(value, root.left));
	    		
	    		
	    	} else {
	    		// still looking -- go right
	    		root.setRight(insertHelper(value, root.right));
	    		
	    	}
	    	updateHeight(root);
    		root = rebalance(root);
	    
	    	return root;
	    }
	}

	////////////////////////////////////////////////////////
	
	//
	// remove()
	// Remove a value from the set if it is present
	//
	public void remove(T value) {
	    this.root = removeHelper(value, this.root);
	}
	
	//
	// removeHelper()
	// Recursive procedure to remove a value from the
	// subtree rooted at "root", if it exists.
	//
	// RETURNS root node of subtree after insertion
	//
	// FIXME: add the necessary code to this function
	// to maintain height and rebalance the tree when
	// a node is removed.
	//
	private TreeNode<T> removeHelper(T value, TreeNode<T> root) {

		if (root == null) { // did not find element
			return null;
		} else {
			int comparison = value.compareTo(root.value);

			if (comparison == 0) { // found element to remove
				if (root.left == null || root.right == null) {
					// base case -- root has at most one subtree,
					// so return whichever one is not null (or null
					// if both are)
					size--;
					return (root.left == null ? root.right : root.left);
				} else {
					// node with two subtrees -- replace key
					// with successor and recursively remove
					// the successor.
					T minValue = minValueInSubtree(root.right);
					root.value = minValue;

					root.setRight(removeHelper(minValue, root.right));
					
					
				}
			} else if (comparison < 0) {
				// still looking for element to remove -- go left
				root.setLeft(removeHelper(value, root.left));
				
			} else {
				// still looking for element to remove -- go right
				root.setRight(removeHelper(value, root.right));
				
			}
			updateHeight(root);
			root = rebalance(root);

			return root;
		}
	}

	
	////////////////////////////////////////////////////////
	//
	// INTERNAL METHODS FOR MAINTAINING BALANCE
	//
	
	// updateHeight()
	//
	// Recompute the height of the subtree rooted at "root",
	// assuming that its left and right children (if any)
	// have correct heights for their respective subtrees.
	//
	// EFFECT: Set the root's height field to the updated value
	//
	private void updateHeight(TreeNode<T> root) {
		if (root.left == null && root.right == null) {
			root.height = 0;
		} else if (root.left == null) {
			root.height = root.right.height + 1;
		} else if (root.right == null) {
			root.height = root.left.height + 1;
		} else if (root.left.height > root.right.height) {
	    	root.height = root.left.height + 1;
	    } else {
	    	root.height = root.right.height + 1;
	    }
	}

	//
	// getBalance()
	// Return the balance factor of a subtree rooted at "root"
	// (right subtree height - left subtree height)
	//
	private int getBalance(TreeNode<T> root) {
	    int balindex;
		if (root.right == null && root.left == null) {
			balindex = 0;
	    } else if (root.right == null) {
	    	balindex = 0 - (root.left.height + 1);
	    } else if (root.left == null) {
	    	balindex = root.right.height + 1;
	    } else {
	    	balindex = root.right.height - root.left.height;
	    }
		return balindex;
	}

	//
	// rebalance()
	//
	// Rebalance an AVL subtree, rooted at "root", that has possibly
	// been unbalanced by a single node's insertion or deletion.
	//
	// RETURNS: the root of the subtree after rebalancing
	//
	private TreeNode<T> rebalance(TreeNode<T> root) {
	    int balindex = getBalance(root);
	    TreeNode<T> newRoot = null;
	    if (balindex == -2) {
	    	if (getBalance(root.left) == 1) {
	    		root.left = leftRotate(root.left);
	    	} 
	    	newRoot = rightRotate(root);
	    } else if (balindex == 2){
	    	if (getBalance(root.right) == -1) {
	    		root.right = rightRotate(root.right);
	    	}
	    	newRoot = leftRotate(root);
	    } else {
	    	newRoot = root;
	    }
	    return newRoot;
	}
	
	
	//
	// rightRotate()
	// Perform a right rotation on a tree rooted at "root"
	// The tree's root is assumed to have a left child.
	//
	// RETURNS: the new root after rotation.
	//
	private TreeNode<T> rightRotate(TreeNode<T> root) {
		TreeNode<T> newRoot = root.left;
		TreeNode<T> rootLeft;
		if (newRoot.right == null) {
			rootLeft = null;
			root.left = rootLeft;
			newRoot.parent = root.parent;
			newRoot.right = root;
			root.parent = newRoot;
			updateHeight(newRoot.right);
	    	updateHeight(newRoot);
		} else {
			rootLeft = newRoot.right;
			root.left = rootLeft;
			rootLeft.parent = root;
			newRoot.parent = root.parent;
			newRoot.right = root;
			root.parent = newRoot;
			updateHeight(newRoot.right);
	    	updateHeight(newRoot);
		}
	    return newRoot;
	}

	//
	// leftRotate()
	// Perform a left rotation on a tree rooted at "root"
	// The tree's root is assumed to have a right child.
	//
	// RETURNS: the new root after rotation.
	//
	private TreeNode<T> leftRotate(TreeNode<T> root) {
	    TreeNode<T> newRoot = root.right;
	    TreeNode<T> rootRight;
	    if (newRoot.left == null) {
	    	rootRight = null;
	    	root.right = rootRight;
	    	newRoot.parent = root.parent;
	    	newRoot.left = root;
	    	root.parent = newRoot;
	    	updateHeight(newRoot.left);
	    	updateHeight(newRoot);
	    } else {
	    	rootRight = newRoot.left;
	    	root.right = rootRight;
	    	rootRight.parent = root;
	    	newRoot.parent = root.parent;
	    	newRoot.left = root;
	    	root.parent = newRoot;
	    	updateHeight(newRoot.left);
	    	updateHeight(newRoot);
	    }
	    return newRoot;
	    
	}
	
	/////////////////////////////////////////////////////////////
	//
	// METHODS USED TO VALIDATE CORRECTNESS OF TREE
	// (You should not have to touch these)
	//

	//
	// getRoot()
	// Return the root node of the tree (for validation only!)
	//
	public TreeNode<T> getRoot() {
	    return this.root;
	}
	
		
	//
	// enumerate()
	// Return the contents of the tree as an ordered list
	//
	public LinkedList<T> enumerate() {
	    return enumerateHelper(this.root);
	}
	
	//
	// enumerateHelper()
	// Enumerate the contents of the tree rooted at "root" in order
	// as a linked list
	//
	private LinkedList<T> enumerateHelper(TreeNode<T> root) {
	    if (root == null) 
		{
		    return new LinkedList<T>();
		}
	    else
		{
		    LinkedList<T> list = enumerateHelper(root.left);
		    list.addLast(root.value);
		    list.addAll(enumerateHelper(root.right));
		    
		    return list;
		}
	}

}

