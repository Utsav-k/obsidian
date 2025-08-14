## Trie

Sources
1. https://interviews.school/trie
2. https://leetcode.com/discuss/post/931977/beginner-friendly-guide-to-trie-tutorial-rbnm/

### Implementation

```cpp

#include<bits/stdc++.h>
using namespace std;

class Node {
    public:
        unordered_map<char, Node*> children;
        // boolean is standard but we can keep track of the count of particular word with int.
        int isEndOfWord;
        Node() {
            isEndOfWord = 0;
        }
};

class Trie {
    private:
        Node* root;
    public:
        Trie() {
            root = new Node();
        }

        void insert(const string &s) {
            Node* node = root;
            for(char c:s) {
                if(node->children.find(c) == node->children.end()) {
                    node->children[c] = new Node();
                }
                node = node->children[c];
            }
            node->isEndOfWord = node->isEndOfWord + 1;
        }
        // Returns x : x is the number of times the string s present in the trie;
        int search(const string &s) {
            Node* node = root;
            for(char c:s) {
                if(node->children.find(c) == node->children.end()) return 0;
                node = node->children[c];
            }
            return node->isEndOfWord;
        }

        bool startsWith(const string &s) {
            Node* node = root;
            for(char c:s) {
                if(node->children.find(c) == node->children.end()) return false;
                node = node->children[c];
            }
            return true;
        }

        // This is the find out all the words present with given prefix, basically prefix search
        vector<string> suggestions(string s) {
            Node* node = root;
            vector<string> ans;
            for(char c:s) {
                if(node->children.find(c) == node->children.end()) return ans;
                node = node->children[c];
            }
            unordered_map<Node*, bool> vis;
            // Start dfs from the last node found through the prefix.
            dfs(node, ans, s, vis);
            return ans;
        }

        // DFS for prefix search and filling up the result vector.
        void dfs(Node *root, vector<string> &res, string &prefix, unordered_map<Node*, bool> &vis) {
            if(root->children.size() == 0) {
                return;
            }
            vis[root] = true;
            for(char c='a'; c<='z'; c++) {
                if(root->children.find(c) != root->children.end()) {
                    Node* next = root->children[c];
                    if(vis[next] == false) {
                        prefix.push_back(c);
                        if(next->isEndOfWord > 0) res.push_back(prefix);
                        dfs(next, res, prefix, vis);
                        prefix.pop_back();
                    }
                }
            }
        }
};


int main() {
    
    Trie trie;
    trie.insert("hello");
    trie.insert("hi");
    trie.insert("heli");
    trie.insert("hoe");
    trie.insert("house");
    trie.insert("home");
    trie.insert("hom");

    vector<string> res = trie.suggestions("ho");
    for(string s:res) cout<< s << "\n";
    return 0;
}


```

## Problems

https://leetcode.com/problems/maximum-xor-of-two-numbers-in-an-array/ (very good xor problem)

https://leetcode.com/problems/prefix-and-suffix-search/ (hard)

https://leetcode.com/problems/maximum-xor-with-an-element-from-array/description/ (tuff)

https://leetcode.com/problems/word-search-ii/ (V. hard)

https://leetcode.com/problems/concatenated-words/description/ 

https://leetcode.com/problems/longest-word-in-dictionary/description/



