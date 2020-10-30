---
layout: post
title: Laravel tugas reza kampret tengah malam
gh-badge: [star, fork, follow]
tags: [laravel]
comments: true
---

## SOAL :

![](https://i.ibb.co/ZNWTZry/image.png)
![](https://i.ibb.co/mb2gyZT/image.png)
![](https://i.ibb.co/r3P705j/image.png)

## JAWAB

pertama, cara install laravel, buka terminal lalu ketik

`composer global require laravel/installer`

setelah itu buat project dengan perintah

`laravel new frameworkweb`

![](https://i.ibb.co/r578f8x/image.png)

### persoalan pertama `Buatlah tabel mobil, buatan, model dan warna menggunakan LARAVEL dengan perintah migrate`

pertama buat migration buatan, model dan warna dulu

`php artisan make:migration Buatan`

`php artisan make:migration Model`

`php artisan make:migration Warna`

`php artisan make:migration Mobil`

![](https://i.ibb.co/vLrZT41/image.png)

sehingga pada file `database/migrations` tercipta file seperti ini

![](https://i.ibb.co/4mvJh1F/image.png)

isi migrasi table buatan seperti ini 

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class Buatan extends Migration
{
    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        Schema::create('buatan', function (Blueprint $table) {
            $table->id();
            $table->string('buatan');
        });
    }

    /**
     * Reverse the migrations.
     *
     * @return void
     */
    public function down()
    {
        Schema::drop('buatan');
    }
}
```

isi migrasi table model seperti ini 

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class Model extends Migration
{
    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        Schema::create('model', function (Blueprint $table) {
            $table->id();
            $table->string('buatan_id');
            $table->string('model');
        });
    }

    /**
     * Reverse the migrations.
     *
     * @return void
     */
    public function down()
    {
        Schema::drop('model');
    }
}

```

isi migrasi table warna seperti ini 

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class Warna extends Migration
{
    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        Schema::create('warna', function (Blueprint $table) {
            $table->id();
            $table->string('warna');
        });
    }

    /**
     * Reverse the migrations.
     *
     * @return void
     */
    public function down()
    {
        Schema::drop('warna');
    }
}

```

isi migrasi table mobil seperti ini 

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class Mobil extends Migration
{
    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        Schema::create('mobil', function (Blueprint $table) {
            $table->id();
            $table->integer('buatan_id');
            $table->integer('model_id');
            $table->integer('warna_id');
            $table->integer('tahun_buatan');
        });
    }

    /**
     * Reverse the migrations.
     *
     * @return void
     */
    public function down()
    {
        Schema::drop('mobil');
    }
}

```

setelah semua tabel lengkap sekarang membuat relasi antar tabel dengan perintah

`php artisan make:migration Relasi`

![](https://i.ibb.co/vPpHJhv/image.png)

lalu ubah file relasi menjadi seperti ini

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class Relasi extends Migration
{
    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        Schema::table('mobil', function (Blueprint $table) {
            $table->bigInteger('buatan_id')->unsigned()->change();
            $table->foreign('buatan_id')->references('id')->on('buatan')
                ->onUpdate('cascade')->onDelete('cascade');
        });

        Schema::table('mobil', function (Blueprint $table) {
            $table->bigInteger('model_id')->unsigned()->change();
            $table->foreign('model_id')->references('id')->on('model')
                ->onUpdate('cascade')->onDelete('cascade');
        });

        Schema::table('mobil', function (Blueprint $table) {
            $table->bigInteger('warna_id')->unsigned()->change();
            $table->foreign('warna_id')->references('id')->on('warna')
                ->onUpdate('cascade')->onDelete('cascade');
        });
        
    }

    /**
     * Reverse the migrations.
     *
     * @return void
     */
    public function down()
    {
        Schema::table('mobil', function(Blueprint $table) {
            $table->dropForeign('mobil_buatan_id_foreign');
        });
        
        Schema::table('mobil', function(Blueprint $table) {
            $table->dropIndex('mobil_buatan_id_foreign');
        });
        
        Schema::table('mobil', function(Blueprint $table) {
            $table->integer('buatan_id')->change();
        });

        Schema::table('mobil', function(Blueprint $table) {
            $table->dropForeign('mobil_model_id_foreign');
        });
        
        Schema::table('mobil', function(Blueprint $table) {
            $table->dropIndex('mobil_model_id_foreign');
        });
        
        Schema::table('mobil', function(Blueprint $table) {
            $table->integer('model_id')->change();
        });

        Schema::table('mobil', function(Blueprint $table) {
            $table->dropForeign('mobil_warna_id_foreign');
        });
        
        Schema::table('mobil', function(Blueprint $table) {
            $table->dropIndex('mobil_warna_id_foreign');
        });
        
        Schema::table('mobil', function(Blueprint $table) {
            $table->integer('warna_id')->change();
        });
    }
}

```

dan ketik perintah `php artisan migrate` untuk mengeksekusi semua

![](https://i.ibb.co/1d387X9/image.png)

sehingga terbentuk table dengan relasi seperti ini

![](https://i.ibb.co/bFj4Rjj/image.png)
*tabel failed_jobs, migration, password_resets dan users tercipta secara default.

### Disini tabel dan relasi sudah selesai terbuat, sekarang mengisi rekaman mengunakan kelas Seeder, tuliskan langkah-langkahnya.

yang pasti, membuat file seeder terlebih dahulu dengan perintah

`php artisan make:seeder BuatanSeeder`

`php artisan make:seeder ModelSeeder`

`php artisan make:seeder WarnaSeeder`

`php artisan make:seeder MobilSeeder`

![](https://i.ibb.co/dGtR0fq/image.png)

sehingga terbentuk file pada `database/seeders`

![](https://i.ibb.co/JjH91KN/image.png)

sekarang isi code pada masing masing file seeder

isi file pada BuatanSeeder seperti ini 

```php
<?php

namespace Database\Seeders;

use Illuminate\Database\Seeder;
use Illuminate\Support\Facades\DB;

class BuatanSeeder extends Seeder
{
    /**
     * Run the database seeds.
     *
     * @return void
     */
    public function run()
    {
        DB::table('buatan')->insert([
        	'id' => 1,
        	'buatan' => 'Nissan',
        ]);

        DB::table('buatan')->insert([
        	'id' => 2,
        	'buatan' => 'Honda',
        ]);
    }
}

```

isi file pada ModelSeeder seperti ini 

```php
<?php

namespace Database\Seeders;

use Illuminate\Database\Seeder;
use Illuminate\Support\Facades\DB;

class ModelSeeder extends Seeder
{
    /**
     * Run the database seeds.
     *
     * @return void
     */
    public function run()
    {
        DB::table('model')->insert([
        	'id' => 1,
        	'buatan_id' => 1,
        	'model' => 'Pathfinder',
        ]);
        DB::table('model')->insert([
        	'id' => 2,
        	'buatan_id' => 2,
        	'model' => 'Bluebird',
        ]);
        DB::table('model')->insert([
        	'id' => 3,
        	'buatan_id' => 1,
        	'model' => 'Civic',
        ]);
        DB::table('model')->insert([
        	'id' => 4,
        	'buatan_id' => 1,
        	'model' => 'Grand Livina',
        ]);
        DB::table('model')->insert([
        	'id' => 5,
        	'buatan_id' => 2,
        	'model' => 'Accord',
        ]);
        DB::table('model')->insert([
        	'id' => 6,
        	'buatan_id' => 2,
        	'model' => 'CRV',
        ]);
    }
}

```

isi file pada WarnaSeeder seperti ini 

```php
<?php

namespace Database\Seeders;

use Illuminate\Database\Seeder;
use Illuminate\Support\Facades\DB;

class WarnaSeeder extends Seeder
{
    /**
     * Run the database seeds.
     *
     * @return void
     */
    public function run()
    {
        DB::table('warna')->insert([
        	'id' => 1,
        	'warna' => 'Putih',
        ]);
        DB::table('warna')->insert([
        	'id' => 2,
        	'warna' => 'Merah',
        ]);
        DB::table('warna')->insert([
        	'id' => 3,
        	'warna' => 'Biru',
        ]);
    }
}

```

isi file pada MobilSeeder seperti ini 

```php
<?php

namespace Database\Seeders;

use Illuminate\Database\Seeder;
use Illuminate\Support\Facades\DB;

class MobilSeeder extends Seeder
{
    /**
     * Run the database seeds.
     *
     * @return void
     */
    public function run()
    {
        DB::table('mobil')->insert([
        	'id' => 1,
        	'buatan_id' => 1,
        	'model_id' => 1,
        	'warna_id' => 2,
        	'tahun_buatan' => '2003',
        ]);
        DB::table('mobil')->insert([
        	'id' => 2,
        	'buatan_id' => 2,
        	'model_id' => 1,
        	'warna_id' => 3,
        	'tahun_buatan' => '2005',
        ]);
        DB::table('mobil')->insert([
        	'id' => 3,
        	'buatan_id' => 1,
        	'model_id' => 1,
        	'warna_id' => 1,
        	'tahun_buatan' => '2005',
        ]);
        DB::table('mobil')->insert([
        	'id' => 4,
        	'buatan_id' => 1,
        	'model_id' => 4,
        	'warna_id' => 1,
        	'tahun_buatan' => '2014',
        ]);
        DB::table('mobil')->insert([
        	'id' => 5,
        	'buatan_id' => 2,
        	'model_id' => 5,
        	'warna_id' => 1,
        	'tahun_buatan' => '2015',
        ]);
        DB::table('mobil')->insert([
        	'id' => 6,
        	'buatan_id' => 2,
        	'model_id' => 5,
        	'warna_id' => 2,
        	'tahun_buatan' => '2015',
        ]);
    }
}

```

setelah itu eksekusi file seeder dengan perintah 

`php artian db:seed --class=BuatanSeeder`
`php artian db:seed --class=ModelSeeder`
`php artian db:seed --class=WarnaSeeder`
`php artian db:seed --class=MobilSeeder`

![](https://i.ibb.co/8M7hHSJ/image.png)
*disini ada relasi antar table sehingga perlu mengurutkan tabel mana yang diisi terlebih dahulu, jika **tidak ada relasi**, untuk eksekusi file seeder cukup dengan perintah `php artisan db:seed` saja.

ahirnya tabel buatan, model, warna dan mobil terisi

![](https://i.ibb.co/prVBvr3/image.png)

Dah, nomor satu selesai

### Buatlah skrip mengunakan Query Builder pada Laravel untuk menampilkan

#### a. Tahun buatan, model, warna

code menampilkan tahun buatan, model dan warna.

```php
$satu = DB::table('mobil')
        ->join('model', 'mobil.model_id', '=', 'model.id')
        ->join('warna', 'mobil.warna_id', '=', 'warna.id')
        ->select('mobil.tahun_buatan', 'model.model', 'warna.warna')->get();
echo $satu;
```

hasil : 

![](https://i.ibb.co/mHhPTyp/image.png)

#### b. Tahun buatan, model, warna yang tahun buatanya 2015 saja

code menampilkan tahun buatan, model dan warna yang tahub buatanya 2015 saja.

```php
$dua = DB::table('mobil')
        ->join('model', 'mobil.model_id', '=', 'model.id')
        ->join('warna', 'mobil.warna_id', '=', 'warna.id')
        ->select('mobil.tahun_buatan', 'model.model', 'warna.warna')
        ->where('mobil.tahun_buatan', '2015')->get();
echo $dua;
```

hasil : 

![](https://i.ibb.co/Zf7kqLP/image.png)

#### c. Id, buatan, model, warna, tahun buatan

```php
$tiga = DB::table('mobil')
        ->join('model', 'mobil.model_id', '=', 'model.id')
        ->join('warna', 'mobil.warna_id', '=', 'warna.id')
        ->join('buatan', 'mobil.buatan_id', '=', 'buatan.id')
        ->select('mobil.id', 'buatan.buatan', 'model.model', 'warna.warna', 'mobil.tahun_buatan')->get();
echo $tiga;
```

hasil : 

![](https://i.ibb.co/1f65Gy7/image.png)

#### d. Buatlah view mobil.blade.php untuk menampilkan hasil permintaan no 3, seperti berikut:

```php
$tiga = DB::table('mobil')
        ->join('model', 'mobil.model_id', '=', 'model.id')
        ->join('warna', 'mobil.warna_id', '=', 'warna.id')
        ->join('buatan', 'mobil.buatan_id', '=', 'buatan.id')
        ->select('mobil.id as id', 'buatan.buatan', 'model.model', 'warna.warna', 'mobil.tahun_buatan')->get();
return view('empat',['data' => $tiga]);
```

file view bernama empat.blade.php

```html
<table border="1px">
    <tr>
        <th>ID</th>
        <th>BUATAN</th>
        <th>MODEL</th>
        <th>WARNA</th>
        <th>TAHUN BUATAN</th>
    </tr>
    <?php
    foreach ($data as $key) { ?>
        <tr>
            <td><?= $key->id ?></td>
            <td><?= $key->buatan ?></td>
            <td><?= $key->model ?></td>
            <td><?= $key->warna ?></td>
            <td><?= $key->tahun_buatan ?></td>
        </tr>
    <?php } ?>
</table>
```

hasil : 

![](https://i.ibb.co/wCMm7nb/image.png)
