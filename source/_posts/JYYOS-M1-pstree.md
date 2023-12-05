---
title: JYYOS M1 pstree
tags: [目录,操作系统,linux]
copyright: true
toc: true
reward: false
date: 2023-11-17 18:54:29
---

实验主页：[M1: 打印进程树(pstree)](https://jyywiki.cn/OS/2023/labs/M1.html)

## 实验思路

### 进程信息获取

所有进程信息都可以在`/proc`目录中获取

- `/proc/<pid>/stat`
- `/proc/<pid>/status` 其中有ppid, name信息
- `/proc/<pid>/cmd`

```c
int get_proc(int pid, PROC* proc) {
    char path[128];
    snprintf(path, sizeof(path), "/proc/%d/status", pid);

    FILE *file = fopen(path, "r");
    if (NULL == file) {
        #ifdef DEBUG
        perror("fopen");
        #endif
        return -1;
    }

    char line[256];
    int uid = -1;
    int ppid = -1;
    char name[128] = "";
    char exe[128] = "";

    //Reading UID,Name,PPID
    while (fgets(line, sizeof(line), file) != NULL) {
        if (strncmp(line, "Uid:", 4) == 0) {
            if (sscanf(line, "Uid:\t%d", &uid) != 1) {
                #ifdef DEBUG
                perror("Error reading UID");
                #endif
                fclose(file);
                return -1;
            }
        } else if (strncmp(line, "Name:", 5) == 0) {
            if (sscanf(line, "Name:\t%127s", name) != 1) {
                perror("Error reading NAME");
                fclose(file);
                return -1;
            }
        } else if (strncmp(line, "PPid:", 5) == 0) {
            if (sscanf(line, "PPid:\t%d", &ppid) != 1) {
                perror("Error reading PPID");
                fclose(file);
                return -1;
            }
        }
    }
    fclose(file);


    //Reading exe
    // snprintf(path, sizeof(path), "/proc/%d/exe", pid);
    // ssize_t len = readlink(path, exe, sizeof(exe) - 1);
    // if (len == -1) {
    //     perror("readlink");
    //     return -1;
    // }

    // exe[len] = '\0';


    //Reading cmdline
    snprintf(path, sizeof(path), "/proc/%d/cmdline", pid);
    file = fopen(path, "r");
    if (file == NULL) {
        #ifdef DEBUG
        perror("fopen");
        #endif
        return -1;
    }

    char cmdline[1024] = "";
    if (fgets(cmdline, sizeof(cmdline), file) == NULL) {
        fclose(file);
        return -1;
    }
    fclose(file);

    //Reading exe
    int i = 0;
    for (i = 0; ' ' != cmdline[i] && i < 128;) {
        exe[i] = cmdline[i];
        i++;
    }

    exe[i] = '\0';

    snprintf(path, sizeof(path), "/proc/%d/fd", pid);
    DIR* dir = opendir(path);
    char fd[1024] = "";
    if (dir == NULL) {
        #ifdef DEBUG
        perror("opendir");
        #endif
        strcpy(fd, "unknow");

        // return -1;
    }
    else {
        struct dirent *entry;
        while ((entry = readdir(dir)) != NULL) {
            if (strcmp(entry->d_name, ".") == 0 || strcmp(entry->d_name, "..") == 0)
                continue;
            snprintf(fd + strlen(fd), sizeof(fd) - strlen(fd), "%s, ", entry->d_name);
        }

        if (strlen(fd) > 0) {
            fd[strlen(fd) - 2] = '\0';
        }
        closedir(dir);
    }

    //Populate the PROC structure
    proc->pid = pid;
    proc->ppid = ppid;
    proc->effecitveuid = uid;
    strncpy(proc->name, name, sizeof(proc->name));
    strncpy(proc->exe, exe, sizeof(proc->exe));
    strncpy(proc->cmdline, cmdline, sizeof(proc->cmdline));
    strncpy(proc->username, "UNKNOW", sizeof(proc->username));
    strncpy(proc->fd, fd, sizeof(proc->fd));
    return 0;

}
```

### 进程树建立

获取到信息后就是个算法题，如何由乱序结点构建一棵树
我的思路是记录下每一个已经出现的进程和它相应的节点指针，每处理一个新进程，就由它的`ppid`判断父节点是否已经出现，若已经出现则直接添加进父节点的孩子域

下面的实现就简单地用数组做了映射，其实可以选择hash等映射方式

```c
int buildProcTree(PROC* root, PROC* *pProcs) {
    DIR *dir = opendir("/proc");
    if (dir == NULL) {
        perror("opendir");
        return -1;
    }

    struct dirent *entry;
    while ((entry = readdir(dir)) != NULL) {
        if (strcmp(entry->d_name, ".") == 0 || strcmp(entry->d_name, "..") == 0){
            continue;
        }

        int pid = atoi(entry->d_name);
        if (pid <= 0 ) continue;

        PROC* procNode = CreateProcTreeNode(pid);
        if (procNode == NULL) {
            continue;
        }
        // printf("PID: %d\n", pid);
        // print_proc_info(procNode);
        pProcs[pid] = procNode;

        if (pProcs[procNode->ppid] != 0) {
            PROC* parent = pProcs[procNode->ppid];
            procNode->parent = parent;
            addChild(parent, procNode);
        } 

    }

    return 0;
}
```

## 效果演示

![image.png](https://cdn.jsdelivr.net/gh/xiaomo-xty/picturehost@main/img/23/11/02/spstree_demo1.png)

![image.png](https://cdn.jsdelivr.net/gh/xiaomo-xty/picturehost@main/img/23/11/02/spstree_p.png)

![image.png](https://cdn.jsdelivr.net/gh/xiaomo-xty/picturehost@main/img/23/11/02/spstree_default.png)

## 完整代码

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <sys/types.h>
#include <dirent.h>

#define MAX_PID 0xF000
#define MAXN 1024

typedef struct _PROC{
    int pid;
    int ppid;
    char name[128];
    char username[32];
    int effecitveuid;
    char exe[128];
    char cmdline[1024];
    char fd[1024];

    struct _PROC *parent;
    struct _PROC *children;
    struct _PROC *next;
} PROC;


int get_proc(int pid, PROC* proc);
PROC* CreateProcTreeNode(int pid);
int CreateProcTree(PROC* *pRoot);
int buildProcTree(PROC* root, PROC* *pProc);
int destroyProcTree(PROC* root);
void printProcTree(const PROC* root, int depth, int* branchs);
void TEST_get_proc();
void print_proc_info(const PROC* proc);
void print_proc(int pid);

void showProcTree();
void showAllInfo();
void showProcByPid(const int pid);



int main(int argc, char **argv) {
    if (argc == 1) {
        showProcTree();
        return 0;
    }

    const char* optionPattern = "han:p:";

    int o;
    while ((o = getopt(argc, argv, optionPattern)) != -1) {
        switch (o)
        {
        case 'h':
            printf("\
This is a simple tool for show process information.\n\n\
default: Print Process in tree structure.\n\
-a     : Print all process's detail information.\n\
-n     : Print a process's information by specially name.\n\
-p     : Print a process's information by pid.\n");
            break;
        case 'a':
            showAllInfo();
            break;
        case 'p':
            showProcByPid(atoi(optarg));
            break;
        case 'n':
            printf("The option is faker.\n");
            break;
        default:
            showProcTree();
            break;
        }
    }

    return 0;
}

int destroyProcTree(PROC* root) {
    //delete children
    if (root->children != NULL) {
        destroyProcTree(root->children);
    }
    PROC *next = root->next;

    //delete current
    free(root);

    if (next != NULL) {
        destroyProcTree(next);
    }


    return 0;
}

void showProcTree() {
    PROC* tree;
    if (CreateProcTree(&tree) != 0) {
        printf("Building procTree falied!\n");
    }

    int branchs[128] = {0};
    printProcTree(tree, 0, branchs);
    destroyProcTree(tree);
}

void showAllInfo() {
    DIR *dir = opendir("/proc");
    if (dir == NULL) {
        perror("opendir");

    }

    struct dirent *entry;
    while ((entry = readdir(dir)) != NULL) {
        if (strcmp(entry->d_name, ".") == 0 || strcmp(entry->d_name, "..") == 0){
            continue;
        }

        int pid = atoi(entry->d_name);
        if (pid <= 0 ) continue;

        print_proc(pid);

    }

}

void showProcByPid(const int pid_passin) {
    DIR *dir = opendir("/proc");
    if (dir == NULL) {
        perror("opendir");
        return;
    }

    struct dirent *entry;
    while ((entry = readdir(dir)) != NULL) {
        if (strcmp(entry->d_name, ".") == 0 || strcmp(entry->d_name, "..") == 0){
            continue;
        }

        int pid = atoi(entry->d_name);
        if (pid <= 0 ) continue;
        if (pid == pid_passin) {
            print_proc(pid);
            return;
        }


    }
}

size_t readline(char* from, char* to) {
    char* t = from;
    while (*from != '\n' && *from != '\0') {
        *to = *from;
        from++; to++; 
    }
    *to = '\0'; to++;

    return from - t;
}


int get_proc(int pid, PROC* proc) {
    char path[128];
    snprintf(path, sizeof(path), "/proc/%d/status", pid);

    FILE *file = fopen(path, "r");
    if (NULL == file) {
        #ifdef DEBUG
        perror("fopen");
        #endif
        return -1;
    }

    char line[256];
    int uid = -1;
    int ppid = -1;
    char name[128] = "";
    char exe[128] = "";

    //Reading UID,Name,PPID
    while (fgets(line, sizeof(line), file) != NULL) {
        if (strncmp(line, "Uid:", 4) == 0) {
            if (sscanf(line, "Uid:\t%d", &uid) != 1) {
                #ifdef DEBUG
                perror("Error reading UID");
                #endif
                fclose(file);
                return -1;
            }
        } else if (strncmp(line, "Name:", 5) == 0) {
            if (sscanf(line, "Name:\t%127s", name) != 1) {
                perror("Error reading NAME");
                fclose(file);
                return -1;
            }
        } else if (strncmp(line, "PPid:", 5) == 0) {
            if (sscanf(line, "PPid:\t%d", &ppid) != 1) {
                perror("Error reading PPID");
                fclose(file);
                return -1;
            }
        }
    }
    fclose(file);


    //Reading exe
    // snprintf(path, sizeof(path), "/proc/%d/exe", pid);
    // ssize_t len = readlink(path, exe, sizeof(exe) - 1);
    // if (len == -1) {
    //     perror("readlink");
    //     return -1;
    // }

    // exe[len] = '\0';


    //Reading cmdline
    snprintf(path, sizeof(path), "/proc/%d/cmdline", pid);
    file = fopen(path, "r");
    if (file == NULL) {
        #ifdef DEBUG
        perror("fopen");
        #endif
        return -1;
    }

    char cmdline[1024] = "";
    if (fgets(cmdline, sizeof(cmdline), file) == NULL) {
        fclose(file);
        return -1;
    }
    fclose(file);

    //Reading exe
    int i = 0;
    for (i = 0; ' ' != cmdline[i] && i < 128;) {
        exe[i] = cmdline[i];
        i++;
    }

    exe[i] = '\0';

    snprintf(path, sizeof(path), "/proc/%d/fd", pid);
    DIR* dir = opendir(path);
    char fd[1024] = "";
    if (dir == NULL) {
        #ifdef DEBUG
        perror("opendir");
        #endif
        strcpy(fd, "unknow");

        // return -1;
    }
    else {
        struct dirent *entry;
        while ((entry = readdir(dir)) != NULL) {
            if (strcmp(entry->d_name, ".") == 0 || strcmp(entry->d_name, "..") == 0)
                continue;
            snprintf(fd + strlen(fd), sizeof(fd) - strlen(fd), "%s, ", entry->d_name);
        }

        if (strlen(fd) > 0) {
            fd[strlen(fd) - 2] = '\0';
        }
        closedir(dir);
    }

    //Populate the PROC structure
    proc->pid = pid;
    proc->ppid = ppid;
    proc->effecitveuid = uid;
    strncpy(proc->name, name, sizeof(proc->name));
    strncpy(proc->exe, exe, sizeof(proc->exe));
    strncpy(proc->cmdline, cmdline, sizeof(proc->cmdline));
    strncpy(proc->username, "UNKNOW", sizeof(proc->username));
    strncpy(proc->fd, fd, sizeof(proc->fd));
    return 0;

}
void TEST_get_proc() {
    DIR *dir = opendir("/proc");
    if (dir == NULL) {
        perror("opendir");
        return;
    }

    struct dirent *entry;
    while ((entry = readdir(dir)) != NULL) {
        if (strcmp(entry->d_name, ".") == 0 || strcmp(entry->d_name, "..") == 0){
            continue;
        }

        int pid = atoi(entry->d_name); //the character not numbers would be translate to 0
        if (pid > 0) {
            PROC proc_info;
            if (get_proc(pid, &proc_info) != 0) {
                printf("proc opening failed\n");
                continue;
            }

            print_proc_info(&proc_info);

        }
    }


}

//Must free pointer after used
PROC* CreateProcTreeNode(int pid) {
    if (pid <= 0) {
        printf("it's a invalid pid\n");
        return NULL;
    }

    PROC* proc = (PROC*)malloc(sizeof(PROC));
    proc->children = NULL;
    proc->parent = NULL;
    proc->next = NULL;
    if (get_proc(pid, proc) == -1) {
        #ifdef DEBUG
        printf("get proc %d failed!\n", pid);
        #endif
        return NULL;
    }

    return proc;
}

int CreateProcTree(PROC* *pRoot) {
    PROC* pProcs[MAX_PID];
    buildProcTree(*pRoot ,pProcs);

    //return the NO.1 process as root, also systemd
    *pRoot = pProcs[1];
    return 0;
}

int addChild(PROC* root, PROC* child) {
    PROC* t = root->children;
    if (t == NULL)
    {
        root->children = child;
        return 1;
    }

    child->next = root->children;
    root->children = child;

}

int buildProcTree(PROC* root, PROC* *pProcs) {
    DIR *dir = opendir("/proc");
    if (dir == NULL) {
        perror("opendir");
        return -1;
    }

    struct dirent *entry;
    while ((entry = readdir(dir)) != NULL) {
        if (strcmp(entry->d_name, ".") == 0 || strcmp(entry->d_name, "..") == 0){
            continue;
        }

        int pid = atoi(entry->d_name);
        if (pid <= 0 ) continue;

        PROC* procNode = CreateProcTreeNode(pid);
        if (procNode == NULL) {
            continue;
        }
        // printf("PID: %d\n", pid);
        // print_proc_info(procNode);
        pProcs[pid] = procNode;

        if (pProcs[procNode->ppid] != 0) {
            PROC* parent = pProcs[procNode->ppid];
            procNode->parent = parent;
            addChild(parent, procNode);
        } 

    }

    return 0;
}

void print_proc_info(const PROC* proc) {
    printf("Name: %s\n", proc->name);
    printf("PID: %d\n", proc->pid);
    printf("PPID: %d\n", proc->ppid);
    printf("EffectvUID: %d\n", proc->effecitveuid);
    printf("User: %s\n", proc->username);
    printf("EXE: %s\n", proc->exe);
    printf("CMDLINE: %s\n", proc->cmdline);
    printf("FD: %s\n\n", proc->fd);
}

void printProcTree(const PROC* root, int depth, int* branchs) {
    if (root == NULL)
        return;
    for (int i = 0; i < depth - 1; ++i) {
        printf("%*s|",branchs[i], "");
    }
    if (depth != 0)
        printf("%*s|--",branchs[depth-1], "");

    printf("%s (%d)\n", root->name, root->pid);

    branchs[depth] = strlen(root->name)+3 + 5;
    PROC *curProc = root->children;
    while (curProc != NULL) {
        printProcTree(curProc, depth+1, branchs);
        curProc = curProc->next;
    }
}

void print_proc(int pid) {
    char filename[128];
    char buff[2048]={0};
    char key[16];
    int str_len;    

    FILE *fp;
    sprintf(filename, "/proc/%d/status", pid);
    fp = fopen(filename, "r");
    if (fp != NULL) {
        int readlen = 0;
        char line[1024];

        str_len = fread(buff, 1, 2048, fp);
        buff[2047] = '\0';
        if (str_len > 0)
        while(*(buff+readlen) != '\0') {
            if (*(buff+readlen) == '\n') readlen++;
            readlen += readline(buff+readlen, line);
            printf("%s", line);
            readlen += readline(buff+readlen, line);
            printf("%s\n", line);
        }
        printf("\n");
    }
}

// //pass in a pid, respond name would be saved in procName
// void getProcNameByPid(int pid, char* procName) {
//     char path[MAXN];
//     snprintf(path, MAXN, "/proc/%d/cmdline", pid);

//     FILE *file = fopen(path, "r");

//     if (NULL == file) {
//         perror("fopen");
//         return -1;
//     }
// }
```
