```
        public Image GetUserImageColored(string userid, Color color)
        {
            string userpic = DoctorOrderSheetBLL.GetUserSignImage(userid);
            Image signImage = StringToImage(userpic);
            if (signImage != null)
            {
                signImage = this.changecolor(signImage, color.R, color.G, color.B);
            }
         
```