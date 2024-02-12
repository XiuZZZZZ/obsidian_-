```
        public Bitmap changecolor(Image SImage, int l1, int l2, int l3)
        {
            int Height = SImage.Height;
            int Width = SImage.Width;
            Bitmap bitmap = new Bitmap(Width, Height);
            Bitmap MyBitmap = (Bitmap)SImage;
            for (int x = 0; x < Width; x++)
            {
                for (int y = 0; y < Height; y++)
                {
                    Color pixel1 = MyBitmap.GetPixel(x, y);
                    int r = pixel1.R;
                    int g = pixel1.G;
                    int b = pixel1.B;
                    if (r + g + b < 250 * 3)
                    {
                        r = l1;
                        g = l2;
                        b = l3;
                        if (r > 255)
                        {
                            r = 255;
                        }
                        if (r < 0)
                        {
                            r = 0;
                        }
                        if (g > 255)
                        {
                            g = 255;
                        }
                        if (g < 0)
                        {
                            g = 0;
                        }
                        if (b > 255)
                        {
                            b = 255;
                        }
                        if (b < 0)
                        {
                            b = 0;
                        }
                        bitmap.SetPixel(x, y, Color.FromArgb(r, g, b));
                    }
                }
            }
            return bitmap;
        }
```