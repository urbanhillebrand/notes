# perl

* **Sort hash by val**
```
foreach my $name (sort { $count{$a} <=> $count{$b} or $a cmp $b } keys %count) {
    printf "%-8s %s\n", $name, $count{$name};
}
```
