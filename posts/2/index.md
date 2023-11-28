# how to use hugo blog? (for me)

<!--more-->

# set blog
you can set your blog through /themes/LoveIt/config.toml

# Writing
write post /content/posts using md

{{< admonition tip "Writing tip" true>}}
https://hugoloveit.com/theme-documentation-extended-shortcodes/#4-admonition
{{< /admonition >}}

# local server
```bash
hugo server
```
Entering hugo server opens the server locally and is accessible at localhost:1313.

# upload
```bash
hugo -t LoveIt # build
cd public
git add .
git commit -m "commit message"
git push origin main

cd ..
git add .
git commit -m "commit message"
git push origin master
```

# set font

![image](https://github.com/neutrinox4b1/neutrinox4b1.github.io/assets/101745344/35138cf0-e1ff-4ba5-8b8a-c2c1a76d4d8d)
how to set font? where is css file?


# reference
- [hugo blog](https://hugoloveit.com/posts/)
