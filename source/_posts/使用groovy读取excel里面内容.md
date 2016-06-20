title: 使用groovy读取excel里面内容
date: 2015-11-20 16:27:54
categories: groovy
keywords: groovy
tags: groovy
---
# 使用groovy读取excel里面内容

### 需要依赖
[apache.org/poi](http://www.apache.org/dyn/closer.lua/poi/release/bin/poi-bin-3.13-20150929.zip)

### 项目目录结构：
![](http://7xlcno.com1.z0.glb.clouddn.com/groovy_excelgroovy_excel_1.png)

项目 assets 下面放了一个people.xlsx文件

![](http://7xlcno.com1.z0.glb.clouddn.com/groovy_excelgroovy_excel_2.png)

### **PS**：
- 本文依赖 [Groovy读取excel文件](http://blog.csdn.net/andyxuq/article/details/7916098) 尝试读取后，发现不能运行
- 下载 Apache的POI组建 遇到问题

### 项目的源码：

	import org.apache.poi.ss.usermodel.Row
	import org.apache.poi.xssf.usermodel.XSSFCell
	import org.apache.poi.xssf.usermodel.XSSFRow
	import org.apache.poi.xssf.usermodel.XSSFSheet
	import org.apache.poi.xssf.usermodel.XSSFWorkbook
	/**
	 * Created by WangQing on 15/11/20.
	 */
	class TestGroovy {
	
	
	
	    void updateResourceDate(){
	        def filePath = "./assets/people.xlsx"
	
	
	        File file = new File(filePath)
	
	        FileInputStream is = new FileInputStream(file);
	
	        XSSFWorkbook workbook = new XSSFWorkbook(is);
	        workbook.setMissingCellPolicy(Row.CREATE_NULL_AS_BLANK);
	
	        //循环sheet
	        (0..<workbook.sheetIterator().collect {return it}.@size).each {s->
	            XSSFSheet sheet = workbook.getSheetAt(s);
	            int rows = sheet.physicalNumberOfRows;
	
	            //忽略第一行,标题行
	            (1..<rows).each{r->
	                XSSFRow row = sheet.getRow(r);
	                def cells = row.physicalNumberOfCells;
	
	                (0..<cells).each{c->
	                    XSSFCell cell = row.getCell(c);
	
	                    def name = "";
	
	                    switch (c) {
	                        case 0:
	                            name = "A:"
	                            break;
	                        case 1:
	                            name = "B:"
	                            break
	                        case 2:
	                            name = "C:"
	                            break
	                        case 3:
	                            name = "D:"
	                            break
	                    }
	                    print name + "  "+cell+ ", ";
	
	
	                }
	                println "";
	            }
	        }
	    }
	
	    static main(args) {
	        TestGroovy a = new TestGroovy();
	        a.updateResourceDate();
	    }
	}
	

项目运行结果：
![](http://7xlcno.com1.z0.glb.clouddn.com/groovy_excelgroovy_excel_3.png)

### Apache的POI组建 遇到问题
首先进入网址：[http://www.apache.org/dyn/closer.lua/poi/release/bin/poi-bin-3.13-20150929.zip](http://www.apache.org/dyn/closer.lua/poi/release/bin/poi-bin-3.13-20150929.zip)

下载文件的时候，一般在工程里面依赖的是 jar,但是下面的是 .zip 很是疑惑,这个文件 26.4M，虽然不相信，但是下载下来后解压开，才明白：
![](http://7xlcno.com1.z0.glb.clouddn.com/groovy_excelgroovy_excel_4.png)

其中该项目中使用了
![](http://7xlcno.com1.z0.glb.clouddn.com/groovy_excelgroovy_excel_5.png)
xmlbeans-2.6.0.jar

这个文件在上面的 lib 下面。

因为我使用的mac 2010 office， 所以文件保存的是：.xlsx。开始使用的 

*HSSFRow*  ，发现报错：
	Request processing failed; nested exception is org.apache.poi.poifs.filesystem.OfficeXmlFileException: The supplied data appears to be in the Office 2007+ XML. POI only supports OLE2 Office documents

	POIFSFileSystem excelFile = new POIFSFileSystem(new FileInputStream("xxx.xlsx"));
	HSSFWorkbook wb = new HSSFWorkbook(excelFile);

原因是：
HSSFWorkbook:是操作Excel2003以前（包括2003）的版本，扩展名是.xls 
XSSFWorkbook:是操作Excel2007的版本，扩展名是.xlsx

所以，你在使用的时候，如果是2003版的，将项目的中  XSS 替换成 HSS。