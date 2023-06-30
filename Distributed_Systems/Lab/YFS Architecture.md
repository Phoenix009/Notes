

#### YFS Client:

```cpp
  struct fileinfo
  {
	std::string name;      // name of the file
	unsigned long atime;   // access times
    unsigned long mtime;   // modified time
    unsigned long ctime;   //  time
    std::string content;   // actual contents of the file
    mode_t mode;           // Mode/Permissions of the file
    unsigned long long size;
  };
```

```cpp
  struct dirinfo
  {
    unsigned long atime;
    unsigned long mtime;
    unsigned long ctime;
    std::unordered_map<std::string, unsigned long long> name_to_inum;
  };
```


