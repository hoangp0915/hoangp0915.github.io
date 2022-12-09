# Call interval
```javascript
public destroyed = new Subject<any>();
private listSub: Subscription[] = [];
ngOnInit(): void {
    this.getPracticeContexts();
}
private loadData(skipLoading?: boolean) {
    this.listSub.push(
      this.service
        .getData(skipLoading)
        .subscribe((res) => {
          if (res?.data) {
            this.handleSetData(res.data);
            this.loadDataInterval();
          }
        }),
    );
  }
private loadDataInterval() {
    this.listSub.push(
      timer(AppConstants.PRACTICE_CONTEXT_INTERVAL)
        .pipe(takeUntil(this.destroyed))
        .subscribe((t) => {
          this.loadData(true);
        }),
    );
  }
  ngOnDestroy(): void {
    this.destroyed.next();
    this.destroyed.complete();
    this.listSub.forEach((sub) => sub.unsubscribe());
  }
```

# Merge Array
```javascript
function mergeArrayObject<T>(data: T[], keys: string[], option?: (current: T) => any) {
  const output = [];
  data.forEach((item) => {
    let existing = output.find((v) => {
      return keys.every((k) => v[k] === item[k]);
    });
    if (existing && option) {
      existing = { ...existing, ...option(item) };
      const index = output.findIndex((v) => {
        return keys.every((k) => v[k] === item[k]);
      });
      output[index] = existing;
    } else {
      let choice = {};
      keys.forEach((k) => {
        choice[k] = item[k];
      });
      option && (choice = { ...choice, ...option(item) });
      output.push(choice);
    }
  });
  return output;
}
```
