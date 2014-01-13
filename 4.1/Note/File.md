文件/目录 操作
============

- [确定文件是否存在](#exists)
- [获取文件内容](#get)
- [获取远程文件内容](#getRemote)
- [获取 php 文件的返回值](#getRequire)
- [== require_once](#requireOnce)
- [替换文件内容 == file_put_contents](#put)
- [向文件头部累加内容](#prepend)
- [向文件尾部累加内容](#append)
- [删除文件](#delete)
- [移动文件或重命名 == rename](#move)
- [复制文件 == copy](#copy)
- [获取文件拓展名](#extension)
- [获取文件类型 == filetype](#type)
- [获取文件大小 == filesize](#size)
- [获取文件最后修改时间 == filemtime](#lastModified)
- [判断指定路径是否是一个目录](#isDirectory)
- [判断指定路径是否可写](#isWritable)
- [判断指定路径是否是一个文件](#isFile)
- [寻找与模式匹配的文件路径 == glob](#glob)
- [获取指定目录下所有文件的数组（不包含文件夹）](#files)
- [获取指定目录下所有文件夹和文件的数组（递归）](#allFiles)
- [获取指定目录下所有文件夹的数组](#directories)
- [创建目录](#makeDirectory)
- [复制目录](#copyDirectory)
- [递归删除目录](#deleteDirectory)
- [清除目录下文件](#cleanDirectory)

---

<a name="exists"></a>
##### 确定文件是否存在

    @param  string  $path
    @return bool
    File::exists($path)

<a name="get"></a>
##### 获取文件内容

    @param  string  $path
    @return string
    @throws FileNotFoundException
    File::get($path)

<a name="getRemote"></a>
##### 获取远程文件内容

    @param  string  $path
    @return string
    File::getRemote($path)

<a name="getRequire"></a>
##### 获取 php 文件的返回值

    @param  string  $path
    @return mixed
    @throws FileNotFoundException
    File::getRequire($path)

<a name="requireOnce"></a>
##### == require_once

    @param  string  $file
    @return mixed
    File::requireOnce($file)

<a name="put"></a>
##### 替换文件内容 == file_put_contents

    @param  string  $path
    @param  string  $contents
    @return int
    File::put($path, $contents)

<a name="prepend"></a>
##### 向文件头部累加内容

    @param  string  $path
    @param  string  $data
    @return int
    File::prepend($path, $data)

<a name="append"></a>
##### 向文件尾部累加内容

    @param  string  $path
    @param  string  $data
    @return int
    File::append($path, $data)

<a name="delete"></a>
##### 删除文件

    @param  string|array  $paths
    @return bool
    File::delete($paths)

<a name="move"></a>
##### 移动文件或重命名 == rename

    @param  string  $path
    @param  string  $target
    @return bool
    File::move($path, $target)

<a name="copy"></a>
##### 复制文件 == copy

    @param  string  $path
    @param  string  $target
    @return bool
    File::copy($path, $target)

<a name="extension"></a>
##### 获取文件拓展名

    @param  string  $path
    @return string
    File::extension($path)

<a name="type"></a>
##### 获取文件类型 == filetype

    @param  string  $path
    @return string
    File::type($path)

<a name="size"></a>
##### 获取文件大小 == filesize

    @param  string  $path
    @return int
    File::size($path)

<a name="lastModified"></a>
##### 获取文件最后修改时间 == filemtime

    @param  string  $path
    @return int
    File::lastModified($path)

<a name="isDirectory"></a>
##### 判断指定路径是否是一个目录

    @param  string  $directory
    @return bool
    File::isDirectory($directory)

<a name="isWritable"></a>
##### 判断指定路径是否可写

    @param  string  $path
    @return bool
    File::isWritable($path)

<a name="isFile"></a>
##### 判断指定路径是否是一个文件

    @param  string  $file
    @return bool
    File::isFile($file)

<a name="glob"></a>
##### 寻找与模式匹配的文件路径 == glob

    @param  string  $pattern
    @param  int     $flags
    @return array
    File::glob($pattern, $flags = 0)

<a name="files"></a>
##### 获取指定目录下所有文件的数组（不包含文件夹）

    @param  string  $directory
    @return array
    File::files($directory)

<a name="allFiles"></a>
##### 获取指定目录下所有文件夹和文件的数组（递归）

    @param  string  $directory
    @return array
    File::allFiles($directory)

<a name="directories"></a>
##### 获取指定目录下所有文件夹的数组

    @param  string  $directory
    @return array
    File::directories($directory)

<a name="makeDirectory"></a>
##### 创建目录

    @param  string  $path
    @param  int     $mode
    @param  bool    $recursive
    @param  bool    $force
    @return bool
    File::makeDirectory($path, $mode = 0777, $recursive = false, $force = false)

<a name="copyDirectory"></a>
##### 复制目录

    @param  string  $directory
    @param  string  $destination
    @param  int     $options
    @return bool
    File::copyDirectory($directory, $destination, $options = null)

<a name="deleteDirectory"></a>
##### 递归删除目录

    The directory itself may be optionally preserved.
    @param  string  $directory
    @param  bool    $preserve
    @return bool
    File::deleteDirectory($directory, $preserve = false)

<a name="cleanDirectory"></a>
##### 清除目录下文件

    @param  string  $directory
    @return bool
    File::cleanDirectory($directory)

