# Sample application for cordova issue

## What is this?

This is a sample application of cordova project in which `cordova prepare` can not work. The project's index.html is not copied to android platform's index.html.

## How to reproduce

git clone
```
$ git clone https://github.com/knight9999/sampleUnupdate.git
```

cd
```
$ cd sampleUnupdate
```

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

The latter platform's `index.html` is different from the former project's `www/index.html`.
The latter platform's `index.html` is still template default. 

So the `cordova prepare` does not update `platforms/android/app/src/main/assets/www/index.html` anymore.

## Reason

The `www/index.html` of this project is `2481' bytes.
```
$ ls -al www/index.html 
-rw-r--r--  1 knaito  staff  2481  1 17 18:02 www/index.html
```

This file size is accidentally same as `cordova-android/bin/templates/project/assets/www/index.html` file size.

Therefore

The following `if` condition in `FileUpdater.js` can not detect the modification of project `index.html`.

- The timestamp of project index.html is older than platform index.html.
- The filesize of project index.html is same as platform index.html

```
                if (sourceStats.mtime.getTime() >= targetStats.mtime.getTime() ||
                        sourceStats.size !== targetStats.size) {
                    log('copy  ' + sourcePath + ' ' + targetPath + ' (updated file)');
                    fs.copySync(sourceFullPath, targetFullPath);
                    updated = true;
}
```

https://github.com/apache/cordova-common/blob/470718c5055d95041fc935daa862967156ae1e23/src/FileUpdater.js#L100


If you edit project index.html (update timestamp), the `cordova prepare` works well.




