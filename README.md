# Sample application for cordova issue

## How to reproduce


install
```
$ npm install
```

prepare
```
$ npx cordova prepare android
```

check the project index.html
```
$ cat www/index.html
```

check platform index.html
```
$ cat platforms/android/app/src/main/assets/www/index.html 
```

The index.html is template default. 
This is different from `www/index.html`.

```
$ npx cordova prepare
```
does not update `platforms/android/app/src/main/assets/www/index.html`.

## Reason

The `www/index.html` of this project is `2481' bytes.
```
knaito-MacBook-Pro:sampleUnupdate knaito$ ls -al www/index.html 
-rw-r--r--  1 knaito  staff  2481  1 17 18:02 www/index.html
```

This is accidentally same as `cordova-android/bin/templates/project/assets/www/index.html` file size.

Therefore

The following `if` condition in `FileUpdater.js` can not detect the modification.

```
                if (sourceStats.mtime.getTime() >= targetStats.mtime.getTime() ||
                        sourceStats.size !== targetStats.size) {
                    log('copy  ' + sourcePath + ' ' + targetPath + ' (updated file)');
                    fs.copySync(sourceFullPath, targetFullPath);
                    updated = true;
}
```

https://github.com/apache/cordova-common/blob/470718c5055d95041fc935daa862967156ae1e23/src/FileUpdater.js#L100



