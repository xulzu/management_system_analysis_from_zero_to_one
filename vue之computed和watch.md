# computed

![图 1](images/1f2a0010dd40563088d36532bf1519c3f430dfb30416d98ceffc4748818cd451.png)

  

> computed的getter

```
 function computedGetter () {
    const watcher = this._computedWatchers && this._computedWatchers[key]
    if (watcher) {
      if (watcher.dirty) {
        watcher.evaluate()
      }
      if (Dep.target) {
        watcher.depend()
      }
      return watcher.value
    }
  }
```

> computed的watcher的一些方法

```
//computed的watcher的lazy在初始化时是true。 当上游的watcher发生变化时，会把dirty修改为true。只有当再次读取给computed的值触发getter时才会重新计算出正确的值出来。
{
  update () {
    /* istanbul ignore else */
    if (this.lazy) {
      this.dirty = true
    } else if (this.sync) {
      this.run()
    } else {
      queueWatcher(this)
    }
  },
  evaluate () {
    this.value = this.get()
    this.dirty = false
  }
}

```
