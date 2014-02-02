# 文件/目录 操作

## 确定文件是否存在

    @param  string  $path
    @return bool
    File::exists($path)

## 获取文件内容

    @param  string  $path
    @return string
    @throws FileNotFoundException
    File::get($path)

## 获取远程文件内容

    @param  string  $path
    @return string
    File::getRemote($path)

## 获取 php 文件的返回值

    @param  string  $path
    @return mixed
    @throws FileNotFoundException
    File::getRequire($path)

## == require_once

    @param  string  $file
    @return mixed
    File::requireOnce($file)

## 替换文件内容 == file_put_contents

    @param  string  $path
    @param  string  $contents
    @return int
    File::put($path, $contents)

## 向文件头部累加内容

    @param  string  $path
    @param  string  $data
    @return int
    File::prepend($path, $data)

## 向文件尾部累加内容

    @param  string  $path
    @param  string  $data
    @return int
    File::append($path, $data)

## 删除文件

    @param  string|array  $paths
    @return bool
    File::delete($paths)

## 移动文件或重命名 == rename

    @param  string  $path
    @param  string  $target
    @return bool
    File::move($path, $target)

## 复制文件 == copy

    @param  string  $path
    @param  string  $target
    @return bool
    File::copy($path, $target)

## 获取文件拓展名

    @param  string  $path
    @return string
    File::extension($path)

## 获取文件类型 == filetype

    @param  string  $path
    @return string
    File::type($path)

## 获取文件大小 == filesize

    @param  string  $path
    @return int
    File::size($path)

## 获取文件最后修改时间 == filemtime

    @param  string  $path
    @return int
    File::lastModified($path)

## 判断指定路径是否是一个目录

    @param  string  $directory
    @return bool
    File::isDirectory($directory)

## 判断指定路径是否可写

    @param  string  $path
    @return bool
    File::isWritable($path)

## 判断指定路径是否是一个文件

    @param  string  $file
    @return bool
    File::isFile($file)

## 寻找与模式匹配的文件路径 == glob

    @param  string  $pattern
    @param  int     $flags
    @return array
    File::glob($pattern, $flags = 0)

## 获取指定目录下所有文件的数组（不包含文件夹）

    @param  string  $directory
    @return array
    File::files($directory)

## 获取指定目录下所有文件夹和文件的数组（递归）

    @param  string  $directory
    @return array
    File::allFiles($directory)

## 获取指定目录下所有文件夹的数组

    @param  string  $directory
    @return array
    File::directories($directory)

## 创建目录

    @param  string  $path
    @param  int     $mode
    @param  bool    $recursive
    @param  bool    $force
    @return bool
    File::makeDirectory($path, $mode = 0777, $recursive = false, $force = false)

## 复制目录

    @param  string  $directory
    @param  string  $destination
    @param  int     $options
    @return bool
    File::copyDirectory($directory, $destination, $options = null)

## 递归删除目录

    The directory itself may be optionally preserved.
    @param  string  $directory
    @param  bool    $preserve
    @return bool
    File::deleteDirectory($directory, $preserve = false)

## 清除目录下文件

    @param  string  $directory
    @return bool
    File::cleanDirectory($directory)

