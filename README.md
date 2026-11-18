using System;
using System.Collections.Generic;

namespace SistemMBG
{
   //soal nomor 1
    public class Sekolah
    {
        private string idSekolah;
        private string namaSekolah;
        private int jumlahPaketDiterima;

        public Sekolah(string idSekolah, string namaSekolah, int jumlahPaketDiterima = 0)
        {
            this.idSekolah = idSekolah;
            this.namaSekolah = namaSekolah;
            this.jumlahPaketDiterima = jumlahPaketDiterima;
        }

        public string IdSekolah => idSekolah;
        public string NamaSekolah => namaSekolah;
        public int JumlahPaketDiterima => jumlahPaketDiterima;

        public void TerimaPaket(int jumlah)
        {
            if (jumlah > 0)
                jumlahPaketDiterima += jumlah;
        }

        public void TampilInfo()
        {
            Console.WriteLine($"ID Sekolah           : {idSekolah}");
            Console.WriteLine($"Nama Sekolah         : {namaSekolah}");
            Console.WriteLine($"Jumlah Paket Diterima: {jumlahPaketDiterima}");
        }
    }

    // soal nomor 2,5, dan 4
    public abstract class PaketMBG
    {
        private string idPaket;
        private string namaPaket;
        private int stokDapur;

        public PaketMBG(string idPaket, string namaPaket, int stokDapur)
        {
            this.idPaket = idPaket;
            this.namaPaket = namaPaket;
            this.stokDapur = stokDapur < 0 ? 0 : stokDapur;
        }

        public string IdPaket
        {
            get { return idPaket; }
            set { idPaket = value; }
        }

        public string NamaPaket
        {
            get { return namaPaket; }
            set { namaPaket = value; }
        }

        public int StokDapur
        {
            get { return stokDapur; }
        }

        public bool KurangiStok(int jumlah)
        {
            if (jumlah <= 0)
                return false;          
            if (jumlah > stokDapur)
                return false;          

            stokDapur -= jumlah;
            return true;
        }

        public abstract int HitungBiaya(int jumlah);
    }

    // soal nomer 3 + Soal nomer 4/5 override HitungBiaya
    public class PaketReguler : PaketMBG
    {
        public const int HargaPerPaket = 15000;

        public string IsiPaket { get; } = "Nasi, Ayam, Sayur, Buah";

        public PaketReguler(string idPaket, string namaPaket, int stokDapur)
            : base(idPaket, namaPaket, stokDapur) { }

        public override int HitungBiaya(int jumlah)
        {
            return jumlah * HargaPerPaket;
        }
    }

    public class PaketKhusus : PaketMBG
    {
        public const int HargaPerPaket = 20000;

        public string JenisDiet { get; set; }

        public PaketKhusus(string idPaket, string namaPaket, int stokDapur, string jenisDiet)
            : base(idPaket, namaPaket, stokDapur)
        {
            JenisDiet = jenisDiet;
        }

        public override int HitungBiaya(int jumlah)
        {
            return jumlah * HargaPerPaket;
        }
    }

    //soal nomer 6
    public interface DapatDidistribusikan
    {
        void KirimPaket();
    }

    public class DistribusiMBG : DapatDidistribusikan
    {
        private Sekolah sekolahTujuan;
        private PaketMBG paket;
        private int jumlah;

        public DistribusiMBG(Sekolah sekolahTujuan, PaketMBG paket, int jumlah)
        {
            this.sekolahTujuan = sekolahTujuan;
            this.paket = paket;
            this.jumlah = jumlah;
        }

        public void KirimPaket()
        {
            if (jumlah <= 0)
            {
                Console.WriteLine("Distribusi MBG gagal! Jumlah paket yang dikirim harus lebih dari 0.");
                return;
            }

            if (jumlah > paket.StokDapur)
            {
                Console.WriteLine($"Distribusi MBG gagal! Jumlah pengiriman ({jumlah}) melebihi stok dapur yang tersedia ({paket.StokDapur}).");
                return;
            }

            paket.KurangiStok(jumlah);
            sekolahTujuan.TerimaPaket(jumlah);
            Console.WriteLine($"Distribusi MBG berhasil! Sebanyak {jumlah} paket makanan telah dikirim ke sekolah {sekolahTujuan.NamaSekolah}");
        }
    }

    //soal nomer 7
    public class PengelolaMBG
    {
        private List<Sekolah> daftarSekolah = new List<Sekolah>();
        private List<PaketMBG> daftarPaket = new List<PaketMBG>();

        public void TambahSekolah(Sekolah sekolah)
        {
            daftarSekolah.Add(sekolah);
        }

        public void TambahPaket(PaketMBG paket)
        {
            daftarPaket.Add(paket);
        }

        public void TampilkanDataMBG()
        {
            Console.WriteLine("=== DAFTAR SEKOLAH PENERIMA ===");
            foreach (var s in daftarSekolah)
            {
                s.TampilInfo();
                Console.WriteLine("---");
            }

            Console.WriteLine("=== DAFTAR PAKET & STOK DAPUR ===");
            foreach (var p in daftarPaket)
            {
                string jenis = p is PaketKhusus pk ? $"Khusus (Diet: {pk.JenisDiet})" : "Reguler";
                Console.WriteLine($"[{p.IdPaket}] {p.NamaPaket} | Jenis: {jenis} | Stok: {p.StokDapur}");
            }
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
            //main soal nomer 1
            Console.WriteLine("##### SOAL 1: Class & Object #####");
            Sekolah sekolah1 = new Sekolah("S001", "SDN 1 Jember", 0);
            Sekolah sekolah2 = new Sekolah("S002", "SMPN 3 Jember", 50);
            sekolah1.TampilInfo();
            Console.WriteLine();
            sekolah2.TampilInfo();
            Console.WriteLine();

            //main soal nomer 2
            Console.WriteLine("##### SOAL 2: Encapsulation #####");
            
            PaketReguler demo = new PaketReguler("P000", "Demo", 10);
            
            Console.WriteLine($"Stok awal: {demo.StokDapur}");
            Console.WriteLine($"KurangiStok(4)  -> {demo.KurangiStok(4)}, stok: {demo.StokDapur}");
            Console.WriteLine($"KurangiStok(-1) -> {demo.KurangiStok(-1)}, stok: {demo.StokDapur}");
            Console.WriteLine($"KurangiStok(99) -> {demo.KurangiStok(99)}, stok: {demo.StokDapur}");
            Console.WriteLine();

            //main soal nomer 3
            Console.WriteLine("##### SOAL 3: Inheritance #####");
            PaketReguler reguler = new PaketReguler("P001", "Paket Reguler", 100);
            PaketKhusus khusus = new PaketKhusus("P002", "Paket Khusus", 40, "Rendah Gula");
            Console.WriteLine($"{reguler.NamaPaket} (isi: {reguler.IsiPaket}), stok {reguler.StokDapur}");
            Console.WriteLine($"{khusus.NamaPaket} (diet: {khusus.JenisDiet}), stok {khusus.StokDapur}");
            Console.WriteLine();

            //main soal nomer 4 dan 5
            Console.WriteLine("##### SOAL 4 & 5: Polymorphism & Abstract Class #####");
           
            PaketMBG[] daftar = { reguler, khusus };   
            foreach (PaketMBG p in daftar)
            {
                Console.WriteLine($"{p.NamaPaket} x 10 = Rp{p.HitungBiaya(10):N0}");
            }
            Console.WriteLine();

            //main soal nomer 6
            Console.WriteLine("##### SOAL 6: Interface (Distribusi) #####");
            Console.WriteLine($"Stok reguler sebelum: {reguler.StokDapur}");

            DapatDidistribusikan d1 = new DistribusiMBG(sekolah1, reguler, 30);   
            d1.KirimPaket();

            DapatDidistribusikan d2 = new DistribusiMBG(sekolah2, reguler, 500);  
            d2.KirimPaket();

            DapatDidistribusikan d3 = new DistribusiMBG(sekolah2, khusus, 0);    
            d3.KirimPaket();

            Console.WriteLine($"Stok reguler sesudah: {reguler.StokDapur}");
            Console.WriteLine();
            sekolah1.TampilInfo();
            Console.WriteLine();
            sekolah2.TampilInfo();
            Console.WriteLine();

            //main soal nomer 7
            Console.WriteLine("##### SOAL 7: PengelolaMBG #####");
            PengelolaMBG pengelola = new PengelolaMBG();
            pengelola.TambahSekolah(sekolah1);
            pengelola.TambahSekolah(sekolah2);
            pengelola.TambahPaket(reguler);
            pengelola.TambahPaket(khusus);
            pengelola.TampilkanDataMBG();

            Console.ReadKey();
        }
    }
}
