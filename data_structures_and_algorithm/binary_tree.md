```c++
#include<iostream>
#include<queue>
using namespace std;

struct TreeNode {
    int val;
    TreeNode* left;
    TreeNode* right;
    TreeNode(int x) : val(x), left(NULL), right(NULL) {}
};

int getOneNodeVal(string& str, int& curPos) {
    int rootVal = 0;
    int nextPos = curPos;
    string valStr;
    // 生成根节点  
    nextPos = str.find_first_of(',', curPos);
    if (nextPos != string::npos) {
        valStr = str.substr(curPos, nextPos - curPos);
    }
    else {
        valStr = str.substr(curPos, str.size() - 1 - curPos);
    }
    rootVal = atoi(valStr.c_str());
    curPos = nextPos;
    return rootVal;
}

TreeNode* stringToBinaryTree(string str) {
    // 空树返回NULL  
    if (str.compare("{}") == 0) {
        return NULL;
    }

    // 生成根节点  
    int rootVal = 0;
    int curPos = 1;
    rootVal = getOneNodeVal(str, curPos);
    TreeNode* root = new TreeNode(rootVal);

    // 初始化广度优先队列  
    vector <TreeNode*> preLevel, curLevel;
    preLevel.push_back(root);

    // 遍历字符串生成二叉树  
    int levelNodeNum = 2;
    int alreadyGenerate = 0;
    TreeNode* pTemp = NULL;
    while (true) {
        // 证明所有节点都生成完毕了  
        if (curPos == string::npos) {
            break;
        }
        curPos++;
        if (str[curPos] == '#') {
            curPos++; pTemp = NULL;
        }
        else {
            rootVal = getOneNodeVal(str, curPos);
            TreeNode* pTemp = new TreeNode(rootVal);
            // left child  
            if (alreadyGenerate % 2 == 0) {
                preLevel[alreadyGenerate / 2]->left = pTemp;
            }
            // right child  
            else {
                preLevel[alreadyGenerate / 2]->right = pTemp;
            }
            curLevel.push_back(pTemp);
        }
        alreadyGenerate++;
        if (alreadyGenerate == levelNodeNum)
        {
            alreadyGenerate = 0;
            preLevel.swap(curLevel);
            curLevel.clear();
            levelNodeNum = preLevel.size() * 2;
        }
    }
    return root;
}


// Binary Tree Level Order Traversal
// https://leetcode.com/problems/binary-tree-level-order-traversal/discuss/33443/C%2B%2B-solution-using-only-one-queue-use-a-marker-NULL
// 从上往下打印出二叉树的每个节点，同层节点从左至右打印。
vector<vector<int>> levelOrder(TreeNode* root)
{
    vector<vector<int> >  result;
    if (!root) return result;
    queue<TreeNode*> q;
    q.push(root);
    q.push(NULL);
    vector<int> cur_vec;
    while (!q.empty()) {
        TreeNode* t = q.front();
        q.pop();
        if (t == NULL) {
            result.push_back(cur_vec);
            cur_vec.resize(0);
            if (q.size() > 0) {
                q.push(NULL);
            }
        }
        else {
            cur_vec.push_back(t->val);
            if (t->left) q.push(t->left);
            if (t->right) q.push(t->right);
        }
    }
    return result;
}


// 从上往下打印出二叉树的每个节点，同层节点从左至右打印。
vector<int> printFromTopToBottom(TreeNode* root) {
    queue<TreeNode*> que;
    vector<int> vec;
    que.push(root); // 先将整个二叉树放入队列
    while (!que.empty()) // 当队列非空进行循环
    {
        TreeNode* p;
        p = que.front(); // 先读取队列的首元素
        que.pop(); // 弹出队列的首元素
        if (p == NULL)
            continue; // 所有元素存入vec后，由于队列中存放着空指针，依然进入循环，但此时p的值为NULL，不执行下面的操作，跳出循环结束
        que.push(p->left);
        que.push(p->right);
        vec.push_back(p->val);
        cout << p->val << endl;
    }
    return vec;
}


// 打印二叉树每层最右端节点的数值
void printRightNode(TreeNode* root) {
    queue<TreeNode*> q1, q2;
    TreeNode* tmp;
    if (root == NULL) {
        return;
    }
    q1.push(root);
    // 只要两队列都不为空
    while (!(q1.empty() && q2.empty())) {
        while (!q1.empty()) {
            tmp = q1.front();
            q1.pop();
            if (tmp->left != NULL) {
                q2.push(tmp->left);
            }
            if (tmp->right != NULL) {
                q2.push(tmp->right);
            }
            if (q1.empty()) {
                cout << tmp->val << endl;
            }
        }
        while (!q2.empty()) {
            tmp = q2.front();
            q2.pop();
            if (tmp->left != NULL) {
                q1.push(tmp->left);
            }
            if (tmp->right != NULL) {
                q1.push(tmp->right);
            }
            if (q2.empty()) {
                cout << tmp->val << endl;
            }
        }
    }
}

int main() {
    /*
                8
             9     7
                 6   5
               3    4
                2
     */
    //string s = "{8,9,7,#,#,6,5,3,#,4,#,#,2}";
    //TreeNode* root = stringToBinaryTree(s);
    TreeNode* root = new TreeNode(8);
    TreeNode* t1 = new TreeNode(9);
    TreeNode* t2 = new TreeNode(7);
    TreeNode* t3 = new TreeNode(6);
    TreeNode* t4 = new TreeNode(5);
    TreeNode* t5 = new TreeNode(3);
    TreeNode* t6 = new TreeNode(4);
    TreeNode* t7 = new TreeNode(2);
    root->left = t1;
    root->right = t2;
    t2->left = t3;
    t2->right = t4;
    t3->left = t5;
    t4->left = t6;
    t5->right = t7;
    vector<int> vectInt = printFromTopToBottom(root);
    std::cout << "------------------------" << std::endl;
    printRightNode(root);
    std::cout << "------------------------" << std::endl;
    return 0;
}


```