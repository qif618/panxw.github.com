---
layout: post
category: "web"
title:  "java文件上传"
tags: [Web]
---
## jsp: ##

    <form action="fileupload" method="post" enctype="multipart/form-data" name="form1">  
  	  <input type="file" name="file">  
    	    <input type="file" name="file">
   	   <input type="file" name="file">
   	   <input type="submit" name="Submit" value="upload">  
    </form>    

<!-- more -->
## servlet:   

    public void doPost(HttpServletRequest request, HttpServletResponse response)  
    throws ServletException, IOException {  
	    DiskFileItemFactory df = new DiskFileItemFactory();  
	    //设定阀值1M，如果超过这个值，则文件就直接写到临时文件，不会一直占用内存  
	    //利用这个特性可在上传大文件的时候不会占用大量内存，可以提高并发使用量。  
	    df.setSizeThreshold(1024 * 1024);  
	      
	    try {  
		    File f = new File("E:\\upload\\temp");  
		    if (!f.exists()) {  //如果文件夹不存在，创建
		    f.mkdirs();  
	    }  
	      
	    df.setRepository(f);  
	    ServletFileUpload sf = new ServletFileUpload(df);  
	    sf.setFileSizeMax(1024 * 1024 * 100);//上传文件最大为100M  
	    List list = sf.parseRequest(request);  
	    for (int i = 0; i < list.size(); i++) {  
		    FileItem fileItem = (FileItem) list.get(i);  
		    String fileName = fileItem.getName();  
		    if(fileName == null || "".equals(fileName.trim())){  
		   	 continue;  
		    }  
		    File ff = new File("E:\\upload\\files");  
		    if (!ff.exists()) {  
		  	  ff.mkdirs();  
		    }  
		      
		    File f2 = new File(ff, fileName);  
		    fileItem.write(f2);  
	    }  
	    } catch (Exception e) {  
	    e.printStackTrace();  
	    }  
	      
	    response.setContentType("text/html");  
	    response.setCharacterEncoding("GBK");  
	    PrintWriter out = response.getWriter();  
	    out.println("<script>alert('文件上传成功！');</script>");  
	    out.flush();  
	    out.close();  
    }  