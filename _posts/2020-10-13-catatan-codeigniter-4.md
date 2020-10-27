---
layout: post
title: Catatan Codeigniter 4
gh-badge: [star, fork, follow]
tags: [codeigniter4]
comments: true
---

### COMPOSER

`composer update --no-dev`


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
                'unique'         => true,
			],
			'alamat' => [
				'type'           => 'VARCHAR',
				'constraint'     => '255',
				'null'           => true,
			],
            'status'      => [
                'type'           => 'ENUM',
                'constraint'     => ['publish', 'pending', 'draft'],
                'default'        => 'pending',
            ],
            'product_description'   => [
                'type'              => 'TEXT',
                'null'              => TRUE,
            ],
            'create_at timestamp default current_timestamp',
			'update_at timestamp default current_timestamp on update current_timestamp',
			'delete_at'   => [
				'type'     => 'datetime',
				'null'     => TRUE,
			],
		]);
		$this->forge->addKey('id', true);
        $this->forge->addForeignKey('category_id','categories','category_id','CASCADE','CASCADE');
		$this->forge->createTable('people');
	}

	//--------------------------------------------------------------------

	public function down()
	{
		$this->forge->dropTable('people');
	}
}
```

Nambah kolom baru di table yang sudah ada.

`php spark migrate:create addEmail`

```php
<?php
 
namespace App\Database\Migrations;
 
use CodeIgniter\Database\Migration;
 
class AddEmail extends Migration
{
    public function up()
    {
        $this->forge->addColumn('pegawai', [
            'email' => [
                'type'           => 'VARCHAR',
                'constraint'     => '100',
                'after'          => 'alamat'
            ]
        ]);
    }
 
    //--------------------------------------------------------------------
 
    public function down()
    {
        $this->forge->dropColumn('pegawai', 'email');
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

![](https://i.ibb.co/2FsZRBQ/image.png)

Array to string conversion
/var/www/html/blog/system/Database/BaseBuilder.php - 2119

Error disebabkan karna ada yang salah didalam $data dalam kasus saya nama field harusnya name saya tulis nama

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


### TEMPLATE MODELS

Copas code bawah, jan lupa ganti nama tabel

nama file `Transaksi_model.php`

```php
<?php namespace App\Models;
use CodeIgniter\Model;
 
class Transaksi_model extends Model
{
    protected $table = 'transaksi';

    public function getSql($sql){
        return $this->db->query($sql)->getResultArray();
    }
     
    public function getTransaksi($id = false)
    {
        if($id === false){
            return $this->findAll();
        }else{
            return $this->getWhere(['id' => $id]);
        }
    }

    public function simpan($data){
        $query = $this->db->table($this->table)->insert($data);
        return $query;
    }

    public function ubah($data, $id)
    {
        $query = $this->db->table($this->table)->update($data, array('id' => $id));
        return $query;
    }

    public function hapus($id)
    {
        $query = $this->db->table($this->table)->delete(array('id' => $id));
        return $query;
    } 
 
}
```


### TEMPLATE CONTROLLER

```php
<?php namespace App\Controllers;
 
use CodeIgniter\Controller;
use App\Models\Admin_model;
 
class Admin extends Controller
{
    private $adminM;

    public function __construct(){
        $this->adminM = new Admin_model();
    }

    public function index()
    {
        $data['admin'] = $this->adminM->getAdmin();
        echo view('admin',$data);
    }
}
```

### TEMPLATE INSERT UPDATE

```php
public function aksi(){
        if($this->request->getPost('status')=="tambah"){
            $pw = password_hash($this->request->getPost('password'),PASSWORD_DEFAULT);
            $data = array(
                'nama'  => $this->request->getPost('nama'),
                'username'  => $this->request->getPost('username'),
                'password'  => $pw,
            );
            $status = $this->adminM->simpan($data);
            session()->setFlashdata('info', 'Berhasil menyimpan data');
        }elseif($this->request->getPost('status')=="ubah"){
            $id = $this->request->getPost('id');
            if(empty($this->request->getPost('password'))){
                $data = array(
                    'nama'  => $this->request->getPost('nama'),
                    'username'  => $this->request->getPost('username'),
                );
                $status = $this->adminM->ubah($data,$id);
            }else{
                $pw = password_hash($this->request->getPost('password'),PASSWORD_DEFAULT);
                $data = array(
                    'nama'  => $this->request->getPost('nama'),
                    'username'  => $this->request->getPost('username'),
                    'password'  => $pw,
                );
                $status = $this->adminM->ubah($data,$id);
            }
            session()->setFlashdata('info', 'Berhasil mengubah data');
        }else{
            session()->setFlashdata('info', 'Terjadi Kesalahan Data');
            return redirect()->to('/admin');
            die();
        }

        if(!$status){
            session()->setFlashdata('info', 'Gagal menyimpan data');
        }
        
        return redirect()->to('/admin');
    }
```
