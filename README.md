# FUSecure

Yuadi adalah seorang developer brilian yang sedang mengerjakan proyek top-secret. Sayangnya, temannya Irwandi yang terlalu penasaran memiliki kebiasaan buruk menyalin jawaban praktikum sistem operasi Yuadi tanpa izin setiap kali deadline mendekat. Meskipun sudah diperingatkan berkali-kali dan bahkan ditegur oleh asisten dosen karena sering ketahuan plagiat, Irwandi tetap tidak bisa menahan diri untuk mengintip dan menyalin jawaban praktikum Yuadi. Merasa kesal karena prestasinya terus-menerus dicuri dan integritasnya dipertaruhkan, Yuadi yang merupakan ahli keamanan data memutuskan untuk mengambil tindakan sendiri dengan membuat FUSecure, sebuah file system berbasis FUSE yang menerapkan akses read-only dan membatasi visibilitas file berdasarkan permission user.

## Deskripsi Tugas

Setelah frustrasi dengan kebiasaan plagiat Irwandi yang merugikan prestasi akademiknya, Yuadi memutuskan untuk merancang sistem keamanan yang sophisticated. Dia akan membuat sistem file khusus yang memungkinkan mereka berdua berbagi materi umum, namun tetap menjaga kerahasiaan jawaban praktikum masing-masing.

### a. Setup Direktori dan Pembuatan User

Langkah pertama dalam rencana Yuadi adalah mempersiapkan infrastruktur dasar sistem keamanannya.

1. Buat sebuah "source directory" di sistem Anda (contoh: `/home/shared_files`). Ini akan menjadi tempat penyimpanan utama semua file.

2. Di dalam source directory ini, buat 3 subdirektori: `public`, `private_yuadi`, `private_irwandi`. Buat 2 Linux users: `yuadi` dan `irwandi`. Anda dapat memilih password mereka.
   | User | Private Folder |
   | ------- | --------------- |
   | yuadi | private_yuadi |
   | irwandi | private_irwandi |

Yuadi dengan bijak merancang struktur ini: folder `public` untuk berbagi materi kuliah dan referensi yang boleh diakses siapa saja, sementara setiap orang memiliki folder private untuk menyimpan jawaban praktikum mereka masing-masing.

![Image](https://github.com/user-attachments/assets/e675b4cc-dd6d-479e-b521-757fe5bcb879)

### b. Akses Mount Point

Selanjutnya, Yuadi ingin memastikan sistem filenya mudah diakses namun tetap terkontrol.

FUSE mount point Anda (contoh: `/mnt/secure_fs`) harus menampilkan konten dari `source directory` secara langsung. Jadi, jika Anda menjalankan `ls /mnt/secure_fs`, Anda akan melihat `public/`, `private_yuadi/`, dan `private_irwandi/`.

![Image](https://github.com/user-attachments/assets/ded35cb1-deb8-4bd2-8cc6-f5acf80c4c77)

### c. Read-Only untuk Semua User

Yuadi sangat kesal dengan kebiasaan Irwandi yang suka mengubah atau bahkan menghapus file jawaban setelah menyalinnya untuk menghilangkan jejak plagiat. Untuk mencegah hal ini, dia memutuskan untuk membuat seluruh sistem menjadi read-only.

1. Jadikan seluruh FUSE mount point **read-only untuk semua user**.
2. Ini berarti tidak ada user (termasuk `root`) yang dapat membuat, memodifikasi, atau menghapus file atau folder apapun di dalam `/mnt/secure_fs`. Command seperti `mkdir`, `rmdir`, `touch`, `rm`, `cp`, `mv` harus gagal semua.

"Sekarang Irwandi tidak bisa lagi menghapus jejak plagiatnya atau mengubah file jawabanku," pikir Yuadi puas.

![Image](https://github.com/user-attachments/assets/d06b12e6-fdfd-49be-9f90-66cb3ffa2453)

### d. Akses Public Folder

Meski ingin melindungi jawaban praktikumnya, Yuadi tetap ingin berbagi materi kuliah dan referensi dengan Irwandi dan teman-teman lainnya.

Setiap user (termasuk `yuadi`, `irwandi`, atau lainnya) harus dapat **membaca** konten dari file apapun di dalam folder `public`. Misalnya, `cat /mnt/secure_fs/public/materi_kuliah.txt` harus berfungsi untuk `yuadi` dan `irwandi`.

![Image](https://github.com/user-attachments/assets/e390129a-b8be-480f-94f5-eddb24852c92)

### e. Akses Private Folder yang Terbatas

Inilah bagian paling penting dari rencana Yuadi - memastikan jawaban praktikum benar-benar terlindungi dari plagiat.

1. File di dalam `private_yuadi` **hanya dapat dibaca oleh user `yuadi`**. Jika `irwandi` mencoba membaca file jawaban praktikum di `private_yuadi`, harus gagal (contoh: permission denied).
2. Demikian pula, file di dalam `private_irwandi` **hanya dapat dibaca oleh user `irwandi`**. Jika `yuadi` mencoba membaca file di `private_irwandi`, harus gagal.

"Akhirnya," senyum Yuadi, "Irwandi tidak bisa lagi menyalin jawaban praktikumku, tapi dia tetap bisa mengakses materi kuliah yang memang kubuat untuk dibagi."

## Contoh Skenario

Setelah sistem selesai, beginilah cara kerja FUSecure dalam kehidupan akademik sehari-hari:

- **User `yuadi` login:**

  - `cat /mnt/secure_fs/public/materi_algoritma.txt` (Berhasil - materi kuliah bisa dibaca)
  - `cat /mnt/secure_fs/private_yuadi/jawaban_praktikum1.c` (Berhasil - jawaban praktikumnya sendiri)
  - `cat /mnt/secure_fs/private_irwandi/tugas_sisop.c` (Gagal dengan "Permission denied" - tidak bisa baca jawaban praktikum Irwandi)
  - `touch /mnt/secure_fs/public/new_file.txt` (Gagal dengan "Permission denied" - sistem read-only)

- **User `irwandi` login:**
  - `cat /mnt/secure_fs/public/materi_algoritma.txt` (Berhasil - materi kuliah bisa dibaca)
  - `cat /mnt/secure_fs/private_irwandi/tugas_sisop.c` (Berhasil - jawaban praktikumnya sendiri)
  - `cat /mnt/secure_fs/private_yuadi/jawaban_praktikum1.c` (Gagal dengan "Permission denied" - tidak bisa baca jawaban praktikum Yuadi)
  - `mkdir /mnt/secure_fs/new_folder` (Gagal dengan "Permission denied" - sistem read-only)

Dengan sistem ini, kedua mahasiswa akhirnya bisa belajar dengan tenang. Yuadi bisa menyimpan jawaban praktikumnya tanpa khawatir diplagiat Irwandi, sementara Irwandi terpaksa harus mengerjakan tugasnya sendiri namun masih bisa mengakses materi kuliah yang dibagikan Yuadi di folder public.

![Image](https://github.com/user-attachments/assets/e390129a-b8be-480f-94f5-eddb24852c92)

## Catatan

- Anda dapat memilih nama apapun untuk FUSE mount point Anda.
- Ingat untuk menggunakan argument `-o allow_other` saat mounting FUSE file system Anda agar user lain dapat mengaksesnya.
- Fokus pada implementasi operasi FUSE yang berkaitan dengan **membaca** dan **menolak** operasi write/modify. Anda perlu memeriksa **UID** dari user yang mengakses di dalam operasi FUSE Anda untuk menerapkan pembatasan private folder.

# FUSecure.c
```
#define FUSE_USE_VERSION 31
#include <dirent.h>
#include <errno.h>
#include <fcntl.h>
#include <fuse.h>
#include <fuse/fuse.h>
#include <linux/limits.h>
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <sys/stat.h>
#include <sys/time.h>
#include <sys/types.h>
#include <unistd.h>

static const char *source_path = "/home/shared_files";

void mkdir_mountpoint(const char *path) {
  struct stat st = {0};
  if (stat(path, &st) == -1) {
    mkdir(path, 0755);
  }
}

static int checkAccessPerm(const char *path, uid_t uid, gid_t gid) {
  char fullPath[PATH_MAX];
  struct stat st;

  snprintf(fullPath, sizeof(fullPath), "%s%s", source_path, path);
  if (lstat(fullPath, &st) == -1)
    return -errno;

  if (strncmp(path, "/private_yuadi/", 14) == 0 ||
      strcmp(path, "/private_yuadi") == 0) {
    if (uid != 1001)
      return -EACCES;
  }

  if (strncmp(path, "/private_irwandi/", 16) == 0 ||
      strcmp(path, "/private_irwandi") == 0) {
    if (uid != 1002)
      return -EACCES;
  }

  return 0;
}

static int xmp_getattr(const char *path, struct stat *stbuf) {
  int res;
  char fpath[PATH_MAX];

  sprintf(fpath, "%s%s", source_path, path);

  res = lstat(fpath, stbuf);

  if (res == -1)
    return -errno;

  return 0;
}

static int xmp_readdir(const char *path, void *buf, fuse_fill_dir_t filler,
                       off_t offset, struct fuse_file_info *fi) {
  char fpath[PATH_MAX];

  if (strcmp(path, "/") == 0) {
    path = source_path;
    sprintf(fpath, "%s", path);
  } else {
    sprintf(fpath, "%s%s", source_path, path);
  }

  int res = 0;

  DIR *dp;
  struct dirent *de;
  (void)offset;
  (void)fi;

  dp = opendir(fpath);

  if (dp == NULL)
    return -errno;

  while ((de = readdir(dp)) != NULL) {
    char childPath[PATH_MAX];

    snprintf(childPath, sizeof(childPath), "%s%s", path, de->d_name);

    /* if (checkAccessPerm(childPath, fuse_get_context()->uid, */
    /*                     fuse_get_context()->gid) != 0) */
    /*   continue; */

    struct stat st;

    memset(&st, 0, sizeof(st));

    st.st_ino = de->d_ino;
    st.st_mode = de->d_type << 12;
    res = (filler(buf, de->d_name, &st, 0));

    if (res != 0)
      break;
  }

  closedir(dp);

  return 0;
}

static int xmp_read(const char *path, char *buf, size_t size, off_t offset,
                    struct fuse_file_info *fi) {
  char fpath[PATH_MAX];
  if (strcmp(path, "/") == 0) {
    path = source_path;

    sprintf(fpath, "%s", path);
  } else {
    sprintf(fpath, "%s%s", source_path, path);

    int accessGrant =
        checkAccessPerm(path, fuse_get_context()->uid, fuse_get_context()->gid);
    if (accessGrant != 0)
      return accessGrant;
  }

  int res = 0;
  int fd = 0;

  (void)fi;

  fd = open(fpath, O_RDONLY);

  if (fd == -1)
    return -errno;

  res = pread(fd, buf, size, offset);

  if (res == -1)
    res = -errno;

  close(fd);

  return res;
}

static int xmp_open(const char *path, struct fuse_file_info *fi) {
  char fullPath[PATH_MAX];

  snprintf(fullPath, sizeof(fullPath), "%s%s", source_path, path);

  if ((fi->flags & O_ACCMODE) != O_RDONLY)
    return -EACCES;

  int accessGrant =
      checkAccessPerm(path, fuse_get_context()->uid, fuse_get_context()->gid);
  if (accessGrant != 0)
    return accessGrant;

  int res = open(fullPath, fi->flags);
  if (res == -1)
    return -errno;

  close(res);
  return 0;
}

static int xmp_write(const char *path, const char *buf, size_t size,
                     off_t offset, struct fuse_file_info *fi) {
  return -EACCES;
}

static int xmp_create(const char *path, mode_t mode,
                      struct fuse_file_info *fi) {
  return -EACCES;
}

static int xmp_unlink(const char *path) { return -EACCES; }

static int xmp_mkdir(const char *path, mode_t mode) { return -EACCES; }

static int xmp_rmdir(const char *path) { return -EACCES; }

static int xmp_rename(const char *from, const char *to) { return -EACCES; }

static int xmp_truncate(const char *path, off_t size) { return -EACCES; }

static int xmp_utimens(const char *path, const struct timespec ts[2]) {
  return -EACCES;
}

static int xmp_chmod(const char *path, mode_t mode) { return -EACCES; }

static int xmp_chown(const char *path, uid_t uid, gid_t gid) { return -EACCES; }

static struct fuse_operations xmp_oper = {
    .getattr = xmp_getattr,
    .readdir = xmp_readdir,
    .read = xmp_read,
    .open = xmp_open,
    .write = xmp_write,
    .create = xmp_create,
    .unlink = xmp_unlink,
    .mkdir = xmp_mkdir,
    .rmdir = xmp_rmdir,
    .rename = xmp_rename,
    .truncate = xmp_truncate,
    .utimens = xmp_utimens,
    .chmod = xmp_chmod,
    .chown = xmp_chown,
};

int main(int argc, char *argv[]) {
  umask(0);

  if (argc < 2) {
    fprintf(stderr, "Usage: %s <mountpoint> [options]\n", argv[0]);
    return 1;
}


  return fuse_main(argc, argv, &xmp_oper, NULL);
}
```
### 1.`Library`
```
#define FUSE_USE_VERSION 31
#include <dirent.h>
#include <errno.h>
#include <fcntl.h>
#include <fuse.h>
#include <fuse/fuse.h>
#include <linux/limits.h>
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <sys/stat.h>
#include <sys/time.h>
#include <sys/types.h>
#include <unistd.h>

static const char *source_path = "/home/shared_files"; 
```
Direktif `#define FUSE_USE_VERSION 31` digunakan untuk menetapkan versi API FUSE yang digunakan. Header `fuse.h` dan `fuse/fuse.h` menyediakan fungsi dan struktur utama dari FUSE. Library `dirent.h` digunakan untuk membaca isi direktori, sedangkan `errno.h` dan `fcntl.h` menyediakan kode kesalahan dan kontrol akses file. Header `stdio.h`, `stdlib.h`, dan `string.h` mendukung operasi dasar seperti input/output, alokasi memori, dan manipulasi string.

Selain itu, `sys/stat.h` digunakan untuk memperoleh atribut file seperti ukuran dan izin akses, sedangkan `sys/time.h` mendukung pengaturan waktu pada file. Header `sys/types.h` menyediakan tipe data sistem seperti `uid_t` dan `mode_t`, dan `unistd.h` menyertakan fungsi sistem penting seperti `read()`, `write()`, dan `getuid()`. Terakhir, `linux/limits.h` digunakan untuk mendefinisikan batasan sistem seperti panjang maksimal path (`PATH_MAX`). Seluruh header ini saling mendukung untuk membangun sistem file `read-only` dengan kontrol akses berbasis UID.

`static const char *source_path = "/home/shared_files"` mendefinisikan sebuah variabel pointer ke string yang menyimpan path direktori sumber dari file system FUSE. Artinya, seluruh isi file system virtual akan diambil dari direktori asli `/home/shared_files`. Kata kunci `static` membatasi visibilitas variabel hanya dalam file ini, dan `const` menunjukkan bahwa nilai path tidak boleh diubah selama program berjalan.

### 2.`mkdir_mountpoint`
```
void mkdir_mountpoint(const char *path) {
  struct stat st = {0};
  if (stat(path, &st) == -1) {
    mkdir(path, 0755);
  }
}
```
Fungsi `mkdir_mountpoint(const char *path)` bertujuan untuk memastikan bahwa direktori mount point sudah tersedia sebelum proses mounting dilakukan. Fungsi ini bekerja dengan memeriksa keberadaan direktori menggunakan `stat()`. Jika direktori yang dimaksud belum ada ,ditandai dengan stat gagal mengakses direktori tersebut, maka fungsi akan membuatnya menggunakan `mkdir()` dengan hak akses `0755`, yang memberikan izin baca, tulis, dan eksekusi untuk pemilik, serta izin baca dan eksekusi untuk grup dan pengguna lain. Meskipun fungsi ini tidak dipanggil dalam fungsi utama `main()`, ia dapat berguna pada tahap pengembangan awal untuk memastikan mount point tersedia.

###   3.`checkAccessPerm`
```
   - `static int checkAccessPerm(const char *path, uid_t uid, gid_t gid) {
  char fullPath[PATH_MAX];
  struct stat st;
  snprintf(fullPath, sizeof(fullPath), "%s%s", source_path, path);
  if (lstat(fullPath, &st) == -1)
    return -errno;
  if (strncmp(path, "/private_yuadi/", 14) == 0 ||
      strcmp(path, "/private_yuadi") == 0) {
    if (uid != 1001)
      return -EACCES;
  }
  if (strncmp(path, "/private_irwandi/", 16) == 0 ||
      strcmp(path, "/private_irwandi") == 0) {
    if (uid != 1002)
      return -EACCES;
  }
  return 0;`
}`
```
Fungsi `checkAccessPerm(const char *path, uid_t uid, gid_t gid)` digunakan untuk memverifikasi apakah pengguna yang sedang mengakses direktori memiliki hak akses sesuai kebijakan yang telah ditentukan. Fungsi ini pertama-tama membentuk path lengkap menuju file atau direktori dengan menggabungkan source_path dan path dari argumen. Kemudian dilakukan pengecekan keberadaan file atau direktori menggunakan `lstat()`. Jika direktori yang dimaksud merupakan `/private_yuadi` (baik secara langsung maupun subdirektorinya), maka hanya pengguna dengan `UID 1001` yang diperbolehkan mengaksesnya. Hal serupa berlaku untuk `/private_irwandi`, di mana hanya `UID 1002` yang diizinkan. Apabila UID pengguna tidak sesuai, maka fungsi akan mengembalikan nilai `-EACCES` sebagai penanda akses ditolak.

### 4.`xmp_getattr`
```
static int xmp_getattr(const char *path, struct stat *stbuf) {
  int res;
  char fpath[PATH_MAX];

  sprintf(fpath, "%s%s", source_path, path);

  res = lstat(fpath, stbuf);

  if (res == -1)
    return -errno;

  return 0;
}
```
Fungsi `xmp_getattr(const char *path, struct stat *stbuf)` bertanggung jawab dalam mengisi metadata dari file atau direktori, seperti ukuran, jenis file, dan hak akses. Fungsi ini akan membentuk path absolut ke direktori sumber dengan menggabungkan `source_path` dan `path` yang diterima. Setelah itu, fungsi menggunakan `lstat()` untuk mengisi struktur `stat` yang disediakan oleh sistem, dan apabila terjadi kesalahan, akan mengembalikan nilai negatif dari `errno`.

### 5.`xmp_readdir`
```
static int xmp_readdir(const char *path, void *buf, fuse_fill_dir_t filler,
                       off_t offset, struct fuse_file_info *fi) {
  char fpath[PATH_MAX];

  if (strcmp(path, "/") == 0) {
    path = source_path;
    sprintf(fpath, "%s", path);
  } else {
    sprintf(fpath, "%s%s", source_path, path);
  }

  int res = 0;

  DIR *dp;
  struct dirent *de;
  (void)offset;
  (void)fi;

  dp = opendir(fpath);

  if (dp == NULL)
    return -errno;

  while ((de = readdir(dp)) != NULL) {
    char childPath[PATH_MAX];

    snprintf(childPath, sizeof(childPath), "%s%s", path, de->d_name);

    /* if (checkAccessPerm(childPath, fuse_get_context()->uid, */
    /*                     fuse_get_context()->gid) != 0) */
    /*   continue; */

    struct stat st;

    memset(&st, 0, sizeof(st));

    st.st_ino = de->d_ino;
    st.st_mode = de->d_type << 12;
    res = (filler(buf, de->d_name, &st, 0));

    if (res != 0)
      break;
  }

  closedir(dp);

  return 0;
}
```
Fungsi `xmp_readdir(...)` digunakan untuk menampilkan daftar isi suatu direktori. Fungsi ini pertama-tama menentukan path absolut berdasarkan path yang diterima, kemudian membuka direktori tersebut menggunakan `opendir()`. Setelah direktori berhasil dibuka, fungsi akan melakukan iterasi terhadap setiap entri di dalamnya menggunakan `readdir()`. Untuk setiap entri, informasi seperti inode dan tipe file dikumpulkan ke dalam struktur `stat`, lalu dikirim ke kernel melalui fungsi `filler()`. Terdapat bagian komentar dalam kode ini yang memungkinkan filter tambahan untuk menyembunyikan file atau direktori berdasarkan `UID`, meskipun secara default tidak diaktifkan.

### 6.`xmp_read`
```
static int xmp_read(const char *path, char *buf, size_t size, off_t offset,
                    struct fuse_file_info *fi) {
  char fpath[PATH_MAX];
  if (strcmp(path, "/") == 0) {
    path = source_path;

    sprintf(fpath, "%s", path);
  } else {
    sprintf(fpath, "%s%s", source_path, path);

    int accessGrant =
        checkAccessPerm(path, fuse_get_context()->uid, fuse_get_context()->gid);
    if (accessGrant != 0)
      return accessGrant;
  }

  int res = 0;
  int fd = 0;

  (void)fi;

  fd = open(fpath, O_RDONLY);

  if (fd == -1)
    return -errno;

  res = pread(fd, buf, size, offset);

  if (res == -1)
    res = -errno;

  close(fd);

  return res;
}
```
Fungsi `xmp_read(...)` bertugas membaca isi file dari lokasi tertentu di dalam file system virtual. Proses dimulai dengan membentuk path lengkap file. Jika file berada di luar root (`/`), maka fungsi akan memanggil `checkAccessPerm()` untuk memastikan pengguna memiliki hak akses. Apabila akses diberikan, file akan dibuka dalam mode baca `(O_RDONLY)` dan isi file akan dibaca dari offset tertentu menggunakan `pread()`, sehingga tidak mengubah posisi file pointer. Setelah pembacaan selesai, file ditutup dan hasil pembacaan dikembalikan ke kernel.

### 7.`xmp_open`
```
static int xmp_open(const char *path, struct fuse_file_info *fi) {
  char fullPath[PATH_MAX];

  snprintf(fullPath, sizeof(fullPath), "%s%s", source_path, path);

  if ((fi->flags & O_ACCMODE) != O_RDONLY)
    return -EACCES;

  int accessGrant =
      checkAccessPerm(path, fuse_get_context()->uid, fuse_get_context()->gid);
  if (accessGrant != 0)
    return accessGrant;

  int res = open(fullPath, fi->flags);
  if (res == -1)
    return -errno;

  close(res);
  return 0;
}
```
Fungsi `xmp_open(...)` merupakan fungsi validasi akses ketika sebuah file hendak dibuka. Fungsi ini akan menolak permintaan jika file tidak dibuka dalam mode baca saja (misalnya mode tulis atau tulis-baca). Selain itu, fungsi akan memanggil `checkAccessPerm()` untuk memastikan UID pengguna sesuai dengan kebijakan akses. Jika lolos validasi, file akan dibuka dan langsung ditutup kembali. File descriptor tidak disimpan karena pembacaan akan dilakukan dalam fungsi terpisah (`xmp_read()`).

### 8.`Fungsi-fungsi lain`
```
static int xmp_write(const char *path, const char *buf, size_t size,
                     off_t offset, struct fuse_file_info *fi) {
  return -EACCES;
}

static int xmp_create(const char *path, mode_t mode,
                      struct fuse_file_info *fi) {
  return -EACCES;
}

static int xmp_unlink(const char *path) { return -EACCES; }

static int xmp_mkdir(const char *path, mode_t mode) { return -EACCES; }

static int xmp_rmdir(const char *path) { return -EACCES; }

static int xmp_rename(const char *from, const char *to) { return -EACCES; }

static int xmp_truncate(const char *path, off_t size) { return -EACCES; }

static int xmp_utimens(const char *path, const struct timespec ts[2]) {
  return -EACCES;
}

static int xmp_chmod(const char *path, mode_t mode) { return -EACCES; }

static int xmp_chown(const char *path, uid_t uid, gid_t gid) { return -EACCES; }
```
Fungsi-fungsi lain seperti `xmp_write`, `xmp_create`, `xmp_unlink`, `xmp_mkdir`, `xmp_rmdir`, `xmp_rename`, `xmp_truncate`, `xmp_utimens`, `xmp_chmod`, dan `xmp_chown` semuanya dikonfigurasi untuk menolak akses. Hal ini dilakukan dengan mengembalikan nilai `-EACCES` secara langsung. Keputusan ini konsisten dengan tujuan dari sistem berkas ini yang bersifat `read-only`, di mana pengguna tidak diperbolehkan melakukan perubahan terhadap isi direktori maupun file yang berada dalam `source_path`.

### 9.`fuse_operations xmp_oper`
```
static struct fuse_operations xmp_oper = {
    .getattr = xmp_getattr,
    .readdir = xmp_readdir,
    .read = xmp_read,
    .open = xmp_open,
    .write = xmp_write,
    .create = xmp_create,
    .unlink = xmp_unlink,
    .mkdir = xmp_mkdir,
    .rmdir = xmp_rmdir,
    .rename = xmp_rename,
    .truncate = xmp_truncate,
    .utimens = xmp_utimens,
    .chmod = xmp_chmod,
    .chown = xmp_chown,
};
```
Struct `fuse_operations xmp_oper` berisi pemetaan antara operasi file system standar dengan fungsi-fungsi yang telah diimplementasikan. Misalnya, operasi `getattr` akan ditangani oleh `xmp_getattr`, `readdir oleh xmp_readdir`, dan `read` oleh `xmp_read`, sementara operasi lain seperti `write` diarahkan ke fungsi penolak akses. Dengan cara ini, FUSE tahu fungsi mana yang akan dipanggil ketika pengguna melakukan operasi tertentu terhadap file system.

### 10.`main`
```
int main(int argc, char *argv[]) {
  umask(0);

  if (argc < 2) {
    fprintf(stderr, "Usage: %s <mountpoint> [options]\n", argv[0]);
    return 1;
}


  return fuse_main(argc, argv, &xmp_oper, NULL);
}
```
Fungsi `main()` merupakan pintu masuk program. Di dalamnya, `umask(0)` digunakan untuk mengatur agar hak akses file tidak dibatasi oleh umask default sistem. Program kemudian memanggil `fuse_main()` dengan argumen `argc`, `argv`, struktur operasi `xmp_oper`, dan `NULL` sebagai private data. Fungsi `fuse_main()` akan mengambil alih proses dan menjalankan sistem file virtual sesuai dengan operasi yang telah didefinisikan.

---
