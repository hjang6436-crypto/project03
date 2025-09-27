#define _CRT_SECURE_NO_WARNINGS
#include <stdio.h>
#include <string.h>
#include <ctype.h>
#include <stdlib.h>

#define MAX 200


typedef struct {
    char data;
    int left;
    int right;
} Node;

Node tree[MAX];
int nodeCount = 0;


int newNode(char data) {
    tree[nodeCount].data = data;
    tree[nodeCount].left = -1;
    tree[nodeCount].right = -1;
    return nodeCount++;
}


int buildTree(const char* s, int* idx) {
    while (s[*idx] == ' ' || s[*idx] == '(') (*idx)++;

    if (s[*idx] == ')') {  
        (*idx)++;
        return -1;
    }

    if (!isalpha(s[*idx])) return -1;

    int root = newNode(s[*idx]);
    (*idx)++;

    while (s[*idx] == ' ') (*idx)++;

    if (s[*idx] == '(') {
        (*idx)++;
        tree[root].left = buildTree(s, idx); 
        while (s[*idx] == ' ') (*idx)++;
        if (s[*idx] == ')') {
            (*idx)++;
            return root;
        }
        tree[root].right = buildTree(s, idx); 
        while (s[*idx] == ' ') (*idx)++;
        if (s[*idx] == ')') (*idx)++;
    }
    return root;
}




void preorder(int root) {
    int stack[MAX], top = -1;
    if (root == -1) return;
    stack[++top] = root;

    while (top != -1) {
        int node = stack[top--];
        printf("%c ", tree[node].data);

        if (tree[node].right != -1) stack[++top] = tree[node].right;
        if (tree[node].left != -1) stack[++top] = tree[node].left;
    }
}


void inorder(int root) {
    int stack[MAX], top = -1;
    int curr = root;

    while (curr != -1 || top != -1) {
        while (curr != -1) {
            stack[++top] = curr;
            curr = tree[curr].left;
        }
        curr = stack[top--];
        printf("%c ", tree[curr].data);
        curr = tree[curr].right;
    }
}


void postorder(int root) {
    int stack1[MAX], stack2[MAX];
    int top1 = -1, top2 = -1;

    if (root == -1) return;
    stack1[++top1] = root;

    while (top1 != -1) {
        int node = stack1[top1--];
        stack2[++top2] = node;

        if (tree[node].left != -1) stack1[++top1] = tree[node].left;
        if (tree[node].right != -1) stack1[++top1] = tree[node].right;
    }

    while (top2 != -1) {
        int node = stack2[top2--];
        printf("%c ", tree[node].data);
    }
}


int main() {
    char input[1000];
    (void)scanf("%[^\n]", input);


    int idx = 0;
    nodeCount = 0;
    int root = buildTree(input, &idx);

    printf("pre-order: ");
    preorder(root);
    printf("\n");

    printf("in-order: ");
    inorder(root);
    printf("\n");

    printf("post-order: ");
    postorder(root);
    printf("\n");

    return 0;
}
