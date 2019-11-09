批量打包下载文件

```php
$filename = $filename . ".zip";
        $zip = new \ZipArchive;//运用类中需要带"\"
        $zip->open($filename, \ZipArchive::CREATE);//运用类中需要带"\"
        foreach ($source_info as $file) {
            $file = KIS_APPLICATION_ROOT . 'www/' . $file['url'];
            $fileContent = file_get_contents($file);//获取文件内容
            if ($fileContent) {
                $zip->addFromString(basename($file), $fileContent);
            }
        }
        $zip->close();
        header('Content-Type: application/zip');
        header('Content-disposition: attachment; filename=' . $filename);
        header('Content-Length: ' . filesize($filename));
        ob_end_clean();//清空（擦除）缓冲区并关闭输出缓冲
        readfile($filename);
```



切割字符串

```php
explode(".", $source_info[0]['name'])[0]
```

