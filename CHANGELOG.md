### Entrance
Add to src/lib.rs 
```
impl MmapOptions {
    ...
    pub fn map_anon_hugepage(&self) -> Result<MmapMut> {
        MmapInner::map_anon_hugepage(self.len.unwrap_or(0), self.stack, self.locked, self.private).map(|inner| MmapMut { inner: inner })
    }
}
```

### Function
Add to src/unix.rs
```
impl MmapInner {
    pub fn map_anon_hugepage(len: usize, stack: bool, locked: bool, private: bool) -> io::Result<MmapInner> {
        let stack = if stack { MAP_STACK } else { 0 };
        let locked = if locked { MAP_LOCKED } else { 0 };
        let private = if private { libc::MAP_PRIVATE } else { libc::MAP_SHARED };
        MmapInner::new(
            len,
            libc::PROT_READ | libc::PROT_WRITE,
            libc::MAP_HUGETLB | libc::MAP_ANON | stack | locked | private,
            -1,
            0,
        )
    }
}
```
### Cargo.toml
Whole file
