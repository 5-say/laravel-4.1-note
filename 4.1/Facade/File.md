File
============

\Illuminate\Filesystem\Filesystem.php

---

- [确定文件是否存在](#)
- [获取文件内容](#)
- [获取远程文件内容](#)
- [获取 php 文件的返回值](#)
- [统一 API require_once](#)
- [统一 API 替换文件内容 file_put_contents](#)
- [向文件头部累加内容](#)
- [向文件尾部累加内容](#)
- [删除文件](#)
- [统一 API 移动文件或重命名 rename](#)
- [统一 API 复制文件 copy](#)
- [获取文件拓展名](#)
- [统一 API 获取文件类型 filetype 获取文件类型](#)
- [统一 API 获取文件大小 filesize 获取文件大小](#)
- [统一 API 获取文件最后修改时间 filemtime](#)
- [判断指定路径是否是一个目录](#)
- [判断指定路径是否可写](#)
- [判断指定路径是否是一个文件](#)
- [统一 API 寻找与模式匹配的文件路径 glob](#)
- [获取指定目录下所有文件的数组（不包含文件夹）](#)
- [获取指定目录下所有文件夹和文件的数组（递归）](#)
- [获取指定目录下所有文件夹的数组](#)
- [创建目录](#)
- [复制目录](#)
- [递归删除目录](#)
- [清除目录下文件](#)

---

##### 确定文件是否存在

    @param  string  $path
    @return bool
    public function exists($path)

##### 获取文件内容

    @param  string  $path
    @return string
    @throws FileNotFoundException
    public function get($path)

##### 获取远程文件内容

    @param  string  $path
    @return string
    public function getRemote($path)

##### 获取 php 文件的返回值

    @param  string  $path
    @return mixed
    @throws FileNotFoundException
    public function getRequire($path)

##### 统一 API require_once

    @param  string  $file
    @return mixed
    public function requireOnce($file)

##### 统一 API 替换文件内容 file_put_contents

    @param  string  $path
    @param  string  $contents
    @return int
    public function put($path, $contents)

##### 向文件头部累加内容

    @param  string  $path
    @param  string  $data
    @return int
    public function prepend($path, $data)

##### 向文件尾部累加内容

    @param  string  $path
    @param  string  $data
    @return int
    public function append($path, $data)

##### 删除文件

    @param  string|array  $paths
    @return bool
    public function delete($paths)

##### 统一 API 移动文件或重命名 rename

    @param  string  $path
    @param  string  $target
    @return bool
    public function move($path, $target)

##### 统一 API 复制文件 copy

    @param  string  $path
    @param  string  $target
    @return bool
    public function copy($path, $target)

##### 获取文件拓展名

    @param  string  $path
    @return string
    public function extension($path)

##### 统一 API 获取文件类型 filetype 获取文件类型

    @param  string  $path
    @return string
    public function type($path)

##### 统一 API 获取文件大小 filesize 获取文件大小

    @param  string  $path
    @return int
    public function size($path)

##### 统一 API 获取文件最后修改时间 filemtime

    @param  string  $path
    @return int
    public function lastModified($path)

##### 判断指定路径是否是一个目录

    @param  string  $directory
    @return bool
    public function isDirectory($directory)

##### 判断指定路径是否可写

    @param  string  $path
    @return bool
    public function isWritable($path)

##### 判断指定路径是否是一个文件

    @param  string  $file
    @return bool
    public function isFile($file)

##### 统一 API 寻找与模式匹配的文件路径 glob

    @param  string  $pattern
    @param  int     $flags
    @return array
    public function glob($pattern, $flags = 0)

##### 获取指定目录下所有文件的数组（不包含文件夹）

    @param  string  $directory
    @return array
    public function files($directory)

##### 获取指定目录下所有文件夹和文件的数组（递归）

    @param  string  $directory
    @return array
    public function allFiles($directory)

##### 获取指定目录下所有文件夹的数组

    @param  string  $directory
    @return array
    public function directories($directory)

##### 创建目录

    @param  string  $path
    @param  int     $mode
    @param  bool    $recursive
    @param  bool    $force
    @return bool
    public function makeDirectory($path, $mode = 0777, $recursive = false, $force = false)

##### 复制目录

    @param  string  $directory
    @param  string  $destination
    @param  int     $options
    @return bool
    public function copyDirectory($directory, $destination, $options = null)

##### 递归删除目录

    The directory itself may be optionally preserved.
    @param  string  $directory
    @param  bool    $preserve
    @return bool
    public function deleteDirectory($directory, $preserve = false)

##### 清除目录下文件

    @param  string  $directory
    @return bool
    public function cleanDirectory($directory)

