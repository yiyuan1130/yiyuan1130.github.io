---
layout: post
title:  "Unity Texture之 网络下载、本地读取、本地保存 用作Sprite"
date:   2019-06-20 20:03:01 +0800
categories: Unity
---

## 网络下载图片
```
IEnumerator DownloadTexture(string url){
    WWW www = new WWW (url);
    yield return www;
    if (www.isDone) {
        texture = www.texture;
    }
}
```

## 保存图片到本地
```
void SaveTexture(){
    string savePath = Application.persistentDataPath + "/test.png";
    FileStream fs = new FileStream(savePath, FileMode.Open);
    byte[] buffer = new byte[fs.Length];
    fs.Read(buffer, 0, buffer.Length);
    fs.Close();
}
```

## 从本地加载图片并使用

```
void LoadTexture(){
    string savePath = Application.persistentDataPath + "/test.png";
    FileStream fs = new FileStream(savePath, FileMode.Open);
    byte[] buffer = new byte[fs.Length];
    fs.Read(buffer, 0, buffer.Length);
    fs.Close();
    texture = new Texture2D(2, 2);
    var iSLoad = texture.LoadImage(buffer);
    texture.Apply();
    Sprite sprite = Sprite.Create (texture, new Rect(0, 0, texture.width, texture.height), Vector2.zero);
}
```