---
title: 'API Axios'
description: 'Referensi Axios API'
prev_title: 'Permintaan POST'
prev_link: '/docs/post_example'
next_title: 'Instansi Axios'
next_link: '/docs/instance'
---

Permintaan dapat dibentuk dengan memberikan konfigurasi yang relevan ke `axios`.

##### axios(config)

```js
// Mengirimkan permintaan POST
axios({
  method: 'post',
  url: '/user/12345',
  data: {
    firstName: 'Fred',
    lastName: 'Flintstone'
  }
});
```

```js
// Permintaan GET untuk gambar remote dalam Node.js
axios({
  method: 'get',
  url: 'http://bit.ly/2mTM3nY',
  responseType: 'stream'
})
  .then(function (response) {
    response.data.pipe(fs.createWriteStream('ada_lovelace.jpg'))
  });
```

##### axios(url[, config])

```js
// Mengirimkan permintaan GET (metode bawaan)
axios('/user/12345');
```

### Alias metode permintaan

Untuk kenyamanan alias telah diberikan ke seluruh metode permintaan yang didukung.

##### axios.request(config)
##### axios.get(url[, config])
##### axios.delete(url[, config])
##### axios.head(url[, config])
##### axios.options(url[, config])
##### axios.post(url[, data[, config]])
##### axios.put(url[, data[, config]])
##### axios.patch(url[, data[, config]])
##### axios.postForm(url[, data[, config]])
##### axios.putForm(url[, data[, config]])
##### axios.patchForm(url[, data[, config]])

###### NOTE
Di saat menggunakan alias, tidak perlu menyertakan properti `url`, `method`, dan `data` dalam konfigurasi.