---
title: 'Pembatalan'
prev_title: 'Penanganan Galat'
prev_link: '/docs/handling_errors'
next_title: 'Isi URL-Encoding'
next_link: '/docs/urlencoded'
---

## Membatalkan permintaan

Mengatur properti `timeout` dalam Axios call akan menangani timeout terkait **respon**.

Dalam beberapa kasus (seperti koneksi jaringan tidak tersedia) Axios call akan diuntungkan
karena membatalkan **koneksi** lebih awal. Tanpa pembatalan, Axios call dapat tertahan hingga
kode/stack parent mengalami timeout (mungkin dalam beberapa menit di aplikasi sisi server). 

Untuk menutup Axios call, Anda dapat menggunakan metode ini:
- `signal`
- `cancelToken` (kadaluarsa)

Mengombinasikan `timeout` dan metode pembatalan (seperti `signal`) dapat menangani timeout terkait **respon** dan **koneksi**.

### `signal`: AbortController

Mulai `v0.22.0` Axios mendukung [`AbortController`](https://developer.mozilla.org/en-US/docs/Web/API/AbortController) untuk membatalkan permintaan dalam API fetch:

```js
const controller = new AbortController();

axios.get('/foo/bar', {
   signal: controller.signal
}).then(function(response) {
   //...
});
// membatalkan permintaan 
controller.abort()
```

Contoh dengan timeout menggunakan API [`AbortSignal.timeout()`](https://developer.mozilla.org/en-US/docs/Web/API/AbortSignal/timeout) terbaru [nodejs 17.3+]:

```js
axios.get('/foo/bar', {
   signal: AbortSignal.timeout(5000) //Aborts request after 5 seconds
}).then(function(response) {
   //...
});
```

Contoh dengan fungsi pembantu timeout:

```js
function newAbortSignal(timeoutMs) {
  const abortController = new AbortController();
  setTimeout(() => abortController.abort(), timeoutMs || 0);

  return abortController.signal;
}

axios.get('/foo/bar', {
   signal: newAbortSignal(5000) // Membatalkan permintaan setelah 5 detik
}).then(function(response) {
   //...
});
```

### CancelToken `kadaluarsa`

Anda juga dapat membatalkan permintaan dengan *CancelToken*.

> API pembatalan token Axios dibuat berdasarkan [rencana Promise yang dapat dibatalkan](https://github.com/tc39/proposal-cancelable-promises) yang telah ditarik.

> API ini telah kadaluarsa sejak `v0.22.0` dan tidak disarankan untuk digunakan di proyek baru

Anda dapat membuat token pembatalan menggunakan factory `CancelToken.source` sebagai berikut:

```js
const CancelToken = axios.CancelToken;
const source = CancelToken.source();

axios.get('/user/12345', {
  cancelToken: source.token
}).catch(function (thrown) {
  if (axios.isCancel(thrown)) {
    console.log('Request canceled', thrown.message);
  } else {
    // tangani galat
  }
});

axios.post('/user/12345', {
  name: 'new name'
}, {
  cancelToken: source.token
})

// membatalkan permintaan (parameter message bersifat opsional)
source.cancel('Operation canceled by the user.');
```

Anda juga dapat membuat token pembatalan dengan menggunakan fungsi eksekutor pada konstruktor
`CancelToken`: 

```js
const CancelToken = axios.CancelToken;
let cancel;

axios.get('/user/12345', {
  cancelToken: new CancelToken(function executor(c) {
    // Fungsi eksekutor menerima fungsi pembatalan sebagai parameter
    cancel = c;
  })
});

// membatalkan permintaan
cancel();
```

> Catatan: Anda dapat membatalkan beberapa permintaan dengan token pembatalan / sinyal yang sama.

Pada masa perpindahan, Anda dapat menggunakan kedua metode pembatalan, bahkan untuk permintaan yang sama:

```js
const controller = new AbortController();

const CancelToken = axios.CancelToken;
const source = CancelToken.source();

axios.get('/user/12345', {
  cancelToken: source.token,
  signal: controller.signal
}).catch(function (thrown) {
  if (axios.isCancel(thrown)) {
    console.log('Request canceled', thrown.message);
  } else {
    // tangani galat
  }
});

axios.post('/user/12345', {
  name: 'new name'
}, {
  cancelToken: source.token
})

// membatalkan permintaan (parameter message bersifat opsional)
source.cancel('Operation canceled by the user.');
// ATAU
controller.abort(); // (parameter message tidak didukung)
```
