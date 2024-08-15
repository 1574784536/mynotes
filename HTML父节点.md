### HTML父节点

```html
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>菜鸟教程(runoob.com)</title>
<style>
.ancestors *{ 
	display: block;
	border: 2px solid lightgrey;
	color: lightgrey;
	padding: 5px;
	margin: 15px;
}
</style>
<script src="https://cdn.staticfile.org/jquery/1.10.2/jquery.min.js">
</script>
<script>
$(document).ready(function(){
	$("span").parent().parent().css({"color":"red","border":"2px solid red"});
});
</script>
</head>

<body class="ancestors">body (曾曾祖父节点)
	<div style="width:500px;">div (曾祖父节点)
		<ul>ul (祖父节点)  
			<li>li (直接父节点)
				<span>span</span>
			</li>
		</ul>   
	</div>
</body>

</html>
```

![](C:\Users\Administrator\Desktop\TIM截图20190903114905.png)



```
<div class="modal fade" tabindex="-1" role="dialog" id="modalupd">
    <div class="modal-dialog" role="document">
        <div class="modal-content">
            <div class="modal-header" style="background-color: #5BC0DE;border-top-left-radius:5px;border-top-right-radius:5px;">
                <button type="button" class="close" data-dismiss="modal" aria-label="Close"><span aria-hidden="true">&times;</span></NOtton>
                <h4 class="modal-title">修改商品</h4>
            </div>
            <div class="modal-body">
                <form id="updform" method="post">
                    商品名称：<input type="text" name="itemName" class="form-control"><br>
                    商品价格：<input type="text" name="itemPrice" class="form-control"><br>
                </form>
            </div>
            <div class="modal-footer">
                <button type="button" class="btn btn-default" data-dismiss="modal">关闭</NOtton>
                <button type="button" class="btn btn-info" id="btnfixupd">修改</NOtton>
            </div>
        </div><!-- /.modal-content -->
    </div><!-- /.modal-dialog -->
</div><!-- /.modal -->
```



```
$('.btnupd').off('click').on('click',function(){
                var itemName=$(this).parent().parent().prev().prev().children(":first").text();
                var itemPrice=$(this).parent().parent().prev().children(":first").text();
                var id=$(this).prop('id');
                $("#modalupd").modal("show");
                $("#updform input[name='itemName']").val(itemName);
                $("#updform input[name='itemPrice']").val(itemPrice);
                $('#btnfixupd').off('click').on('click',function(){
                    var form=$('#updform').serialize();
                    $("#modalupd").modal("hide");
                    $.ajax({
                        url:'upd_item',
                        method:'post',
                        data:form+'&itemId='+id,
                        success:function(result){
                            if(result.code==200){
                                //alert("修改成功");
                                $('#t1 tr:not(:first)').remove();
                                listItem();
                            }else{
                                alert("修改失败");
                            }
                        }
                    })
                });
            });
```

