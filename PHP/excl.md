```php
<?php
/**
 *  数据导入
 * @param string $file excel文件
 * @param string $sheet
 * @return array
 * @throws PHPExcel_Exception
 * @throws PHPExcel_Reader_Exception
 */
function importExecl($file = '', $sheet = 0)
{
    $file = iconv("utf-8", "gb2312", $file);   //转码
    if (empty($file) OR !file_exists($file)) {
        die('file not exists!');
    }
    include('PHPExcel.php');  //引入PHP EXCEL类
    $objRead = new PHPExcel_Reader_Excel2007();   //建立reader对象
    if (!$objRead->canRead($file)) {
        $objRead = new PHPExcel_Reader_Excel5();
        if (!$objRead->canRead($file)) {
            die('No Excel!');
        }
    }

    $cellName = array('A', 'B', 'C', 'D', 'E', 'F', 'G', 'H', 'I', 'J', 'K', 'L', 'M', 'N', 'O', 'P', 'Q', 'R', 'S', 'T', 'U', 'V', 'W', 'X', 'Y', 'Z', 'AA', 'AB', 'AC', 'AD', 'AE', 'AF', 'AG', 'AH', 'AI', 'AJ', 'AK', 'AL', 'AM', 'AN', 'AO', 'AP', 'AQ', 'AR', 'AS', 'AT', 'AU', 'AV', 'AW', 'AX', 'AY', 'AZ');

    $obj = $objRead->load($file);  //建立excel对象
    $currSheet = $obj->getSheet($sheet);   //获取指定的sheet表
    $columnH = $currSheet->getHighestColumn();   //取得最大的列号
    $columnCnt = array_search($columnH, $cellName);
    $rowCnt = $currSheet->getHighestRow();   //获取总行数

    print_r($columnCnt);
    print_r($rowCnt);

    $data = array();
    for ($_row = 1; $_row <= $rowCnt; $_row++) {  //读取内容
        for ($_column = 0; $_column <= $columnCnt; $_column++) {
            $cellId = $cellName[$_column] . $_row;
            $cellValue = $currSheet->getCell($cellId)->getValue();
//$cellValue = $currSheet->getCell($cellId)->getCalculatedValue();  #获取公式计算的值
            if ($cellValue instanceof PHPExcel_RichText) {   //富文本转换字符串
                $cellValue = $cellValue->__toString();
            }
            $data[$_row][$cellName[$_column]] = $cellValue;
        }
    }
    return $data;
}

try {
    importExecl("员工刷卡记录表10.01-10.31.xls");
} catch (PHPExcel_Reader_Exception $e) {
} catch (PHPExcel_Exception $e) {
}
```

```php
// 内容
//$csv_body = [
//    ['张三', '男', '13'],
//    ['李四', '女', '13'],
//    ['王五', '男', '13'],
//    ['赵六', '未知', '13']
//];
```

