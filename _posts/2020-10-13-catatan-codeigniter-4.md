---
layout: post
title: Catatan Codeigniter 4
gh-badge: [star, fork, follow]
tags: [codeigniter4]
comments: true
---

#### Migration

`php spark migrate:create [nama_table]`

```php
<?php namespace App\Database\Migrations;

use CodeIgniter\Database\Migration;

class Peoples extends Migration
{
	public function up()
	{
		$this->forge->addField([
			'id'          => [
				'type'           => 'INT',
				'constraint'     => 5,
				'unsigned'       => true,
				'auto_increment' => true,
			],
			'nama'       => [
				'type'           => 'VARCHAR',
				'constraint'     => '64',
			],
			'alamat' => [
				'type'           => 'VARCHAR',
				'constraint'     => '255',
				'null'           => true,
			],
		]);
		$this->forge->addKey('id', true);
		$this->forge->createTable('people');
	}

	//--------------------------------------------------------------------

	public function down()
	{
		$this->forge->dropTable('people');
	}
}
```
#### Seeder

ref: https://iniblog.xyz/blogpost/article/87/seeder-and-faker 

Pertama buat file secara manual di app/Database/Seeds namanya SimpleSeeder

lalu isi seperti ini

```php
<?php namespace App\Database\Seeds;

class SimpleSeeder extends \CodeIgniter\Database\Seeder
{
        public function run()
        {
                $data = [
                        [
                                'nama'  => 'Prof. Sua Ambiguu',
                                'alamat'  =>  'Jl Kayu Mati Nomor 45'
                        ],
                        [
                                'nama'  => 'Ir. Soekarno Putro',
                                'alamat'  =>  'Jl Megayu 45'
                        ]
                ];
                
                $this->db->table('people')->insertBatch($data);
        }
}
```
dan perintah untuk eksekusi

`php spark db:seed SimpleSeeder`

ketemu masalah ini

![](https://i.ibb.co/xm8CKyT/image.png)

The specified Seeder is not a valid file: /var/www/html/ci4/app/Database/Seeds/SimpleSeeder.php
/var/www/html/ci4/system/Database/Seeder.php - 159

masalah karena nama file saya simpleSeeder, bukan SimpleSeeder alias caseSensitive anjim

jika ingin memasukkan satu data saja, menggunakan `insert()` bukan `insertBatch()`.

contoh : 

```php
public function run()
{
        $data = [
                'nama'  => 'Prof. Sua Ambiguu',
                'alamat'  =>  'Jl Kayu Mati Nomor 45'
        ];
        
        $this->db->table('people')->insert($data);
}
```

karena jika engga gitu bakal keluar error spt ini

![](https://i.ibb.co/KFNnjqV/image.png)

array_keys() expects parameter 1 to be array, string given
/var/www/html/ci4/system/Database/BaseBuilder.php - 2144

#### Faker

pertama clone faker dari github

buka terminal, masuk ke direktori app/Libraries

`cd app/Libraries/`
`git clone https://github.com/fzaninotto/Faker`

![](https://i.ibb.co/1bB9GSY/image.png)

lalu ubah kode pada SimpleSeeder.php menjadi

```php
<?php namespace App\Database\Seeds;
require_once APPPATH .'Libraries/Faker/src/autoload.php';

class SimpleSeeder extends \CodeIgniter\Database\Seeder
{
        
        public function run()
        {
                $faker = \Faker\Factory::create('id_ID');

                for ($i = 0; $i < 20; $i++) {
                        $data = [
                                'nama'        => $faker->name,
                                'alamat'      => $faker->address,
                        ];

                        $this->db->table('people')->insert($data);
                }
                
        }
}
```

dah.

#### FILTER

https://ilmucoding.com/middleware-filters-codeigniter-4/

buat file LoginFiter.php di App/Filter

```php
<?php namespace App\Filters;

use CodeIgniter\HTTP\RequestInterface;
use CodeIgniter\HTTP\ResponseInterface;
use CodeIgniter\Filters\FilterInterface;

class LoginFilter implements FilterInterface
{
    public function before(RequestInterface $request, $arguments = null)
    {
        // Do something here
        if(!session()->has('logged_in')){
            session()->setFlashdata('info', [0,"Silahkan Login Terlebih Dahulu"]);
            return redirect()->to('/admin');
        }
    }
 
    //--------------------------------------------------------------------
 
    public function after(RequestInterface $request, ResponseInterface $response, $arguments = null)
    {
        // Do something here
    }
}
```

Tambahkan ini di App/Config/Filters.php

```php
public $aliases = [
		'csrf'     => \CodeIgniter\Filters\CSRF::class,
		'toolbar'  => \CodeIgniter\Filters\DebugToolbar::class,
		'honeypot' => \CodeIgniter\Filters\Honeypot::class,
		'logged_in'    => \App\Filters\LoginFilter::class, // <= TAMBAHIN INI BRO
	];
```

Lalu buat aturan di route

```php
$routes->group('', ['filter' => 'logged_in'], function($routes){ // <= KASIH FILTER NYA
	$routes->get('/dashboard', 'Admin::dashboard');
	$routes->get('/logout', 'Admin::logout');	
}); 
```

Dah.

**buat notif**
cari toast bootstrap, sesuaiin tampilanya mau kek gimana.
pastikan toast muncul dulu setiap kali reload page.
kalo ngga muncul, biasanya gara2 ga dipanggil di javascriptnya

`$('.toast').toast('show')`

kalo udah muncul toastnya, baru ngurus datanya.
simpan data di session() kek gini

```php
session()->setFlashdata('info', [0,"Silahkan Login Terlebih Dahulu"]); // <= SIMPEN DI SESSION
```

Trus tampilan toast bootstrap tadi taruh di bawah tag body.
kodenya gini

```html
<body class="hold-transition lockscreen">
<?php if(!empty(session()->getFlashdata('info'))){ ?>
  <div role="alert" class="toast" aria-live="assertive" aria-atomic="true" data-autohide="true" data-delay="3000" style="position: absolute; top: 20px; right: 10px; z-index:999">
        <div class="toast-header">
        <?php if(session()->getFlashdata('info')[0] == 0){ ?>
            <svg class="bd-placeholder-img rounded mr-2" width="20" height="20" xmlns="http://www.w3.org/2000/svg" preserveAspectRatio="xMidYMid slice" focusable="false" role="img"><rect width="100%" height="100%" fill="#f00"></rect></svg>
            <strong class="mr-auto">Gagal</strong>
        <?php }else{ ?>
            <svg class="bd-placeholder-img rounded mr-2" width="20" height="20" xmlns="http://www.w3.org/2000/svg" preserveAspectRatio="xMidYMid slice" focusable="false" role="img"><rect width="100%" height="100%" fill="#007aff"></rect></svg>
            <strong class="mr-auto">Berhasil</strong>
        <?php } ?>
        </div>

        <div class="toast-body">
            <?= session()->getFlashdata('info')[1] ?>
        </div>
  </div>
<?php } ?>
```

Sip.


**pengganti isset() kalau session bisa kek gini**

```php
if ($this->session->has('message')) {
	$dataIndex['message'] = $this->session->get('message');
}
```
