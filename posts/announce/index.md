# how to use hugo blog? (for me)

<!--more-->

# set blog
you can set your blog through /themes/LoveIt/config.toml

# Writing
write post /content/posts using md

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

# reference
- [hugo blog](https://hugoloveit.com/posts/)
