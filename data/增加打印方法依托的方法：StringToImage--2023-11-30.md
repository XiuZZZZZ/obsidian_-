```
        public Image StringToImage(string imgstr)
        { //字符串－－〉流
            if (imgstr == "") return null;
            byte[] ChangeAfterImageBytes = Convert.FromBase64String(imgstr);//再将字符串分拆成字节数组
            MemoryStream ChangeAfterMS = new MemoryStream(ChangeAfterImageBytes);//将字节数组保存到新的内存流上
            Image Image = Image.FromStream(ChangeAfterMS);//将内存流保存成一张图片
            return Image;
        }
```