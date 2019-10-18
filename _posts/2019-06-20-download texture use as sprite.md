---
layout: post
title:  "Unity Texture之 网络下载、本地读取、本地保存 用作Sprite"
date:   2019-06-20 20:03:01 +0800
categories: Unity
---

## 网络下载图片
```
// 2017之后推荐使用UnityWebRequest
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
    // 文件流方式存储本读文件
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
    // 使用文件流读取本地图片文件
    FileStream fs = new FileStream(savePath, FileMode.Open);
    byte[] buffer = new byte[fs.Length];
    fs.Read(buffer, 0, buffer.Length);
    fs.Close();
    // 创建texture并设置图片格式，4通道32位，不使用mipmap
    texture = new Texture2D(1, 1, TextureFormat.ARGB4444, false);
    var iSLoad = texture.LoadImage(buffer);
    texture.Apply();
    // 创建精灵，中心点默认（0, 0）
    Sprite sprite = Sprite.Create (texture, new Rect(0, 0, texture.width, texture.height), Vector2.zero);
}
```