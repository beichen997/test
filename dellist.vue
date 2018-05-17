<template>
    <div >
        <input v-show="false" type="file" :id="filesone" name="file" class="upload__input" @change="uploadChange" accept="image/png,image/gif"> 
       
            <div class="imgSrc" style="width:148px;height:148px;position:relative;border:1px dashed #c0ccda;background:#fbfdff;border-radius:6px;">  
                <span style="white-space:pre;position:absolute;font-size:28px;color: #8c939d;top:60px;left:60px;">
                     <label :for="filesone"><i class="el-icon-plus"></i></label></span> 
                <img :src="imgDataUrl" v-show="imgDataUrl" style="width:148px;height:148px;">  
            </div>  
    </div>
</template>
<script>
const getBase64=function(file,callback){  
            var maxWidth = 640;  
            if(file.files && file.files[0]){  
                var thisFile = file.files[0];  
                if(thisFile.size>2019200){  
                    // mualert.alertBox("图片不能超过800K");  
                    alert("图片不能超过2M");  
                    return  
                };  
                var reader = new FileReader();  
                reader.onload = function(event){  
                    var imgUrl = event.target.result;  
                    var img = new Image();  
                    img.onload = function(){  
                        var canvasId = 'canvasBase64Imgid',  
                        canvas = document.getElementById(canvasId);  
                        if(canvas!=null){document.body.removeChild(canvas);}  
                        var canvas = document.createElement("canvas");  
                        canvas.innerHTML = 'New Canvas';  
                        canvas.setAttribute("id", canvasId);  
                        canvas.style.display='none';  
                        document.body.appendChild(canvas);  
                        canvas.width = this.width;  
                        canvas.height = this.height;  
                        var imageWidth = this.width,   
                        imageHeight = this.height;  
                        if (this.width > maxWidth){  
                            imageWidth = maxWidth;  
                            imageHeight = this.height * maxWidth/this.width;  
                            canvas.width = imageWidth;  
                            canvas.height = imageHeight;  
                        }  
                        var context = canvas.getContext('2d');  
                        context.clearRect(0, 0, imageWidth, imageHeight);  
                        context.drawImage(this, 0, 0, imageWidth, imageHeight);  
                        var base64 = canvas.toDataURL('image/png',1);  
                        var imgbase = base64.substr(22);  
                        callback(imgbase)  
                        //this.imgUrl =   
                    }  
                    img.src = imgUrl;  
                }  
                reader.readAsDataURL(file.files[0]);  
            }  
        };
import { uploadGoodsFile } from "@/api/shopFbu";
export default {
    data(){
        return {
            imgDataUrl:"",
            filesone:this.id || 'vue-filesone-' + +new Date()+ +Math.random(),
        }
    },
    methods: {
        uploadChange(event){
            console.log(this.filesone,"filesone");
            if(event.target.files.length>0){  
                this.files = event.target.files[0];  //提交的图片  
                // getBase64(event.target,(url)=>{  
                //     this.imgDataUrl = 'data:image/png;base64,'+url;   //显示的图片  
                //     // console.log( this.imgDataUrl," this.imgDataUrl");
                // }); 
                //提交表单，进行上传
                let data  = {};  
                let files = this.files;  
                let param = new FormData(); //创建form对象  
                if(files!=''){  
                    param.append('file', files,files.name); //单个图片 ，多个用循环 append 添加  
                }else{  
                    this.$message.error('请添加图片');  
                }  
                 param.append('tenantCode','00000001');
                param.append('param', JSON.stringify(data));//添加form表单中其他数据 
                // formDataID.append('file',this.$refs.detl.value);
               console.log(param,"param");
                uploadGoodsFile(param).then(res=>{  //请求接口
                 this.imgDataUrl = ''; 
                this.imgDataUrl = res.fileUrl ; //action地址
                    console.log(res,8899);
                   
                    this.$emit('dellist',res.fileUrl);
                });   
            }  
          
        },  
    }
}
</script>
