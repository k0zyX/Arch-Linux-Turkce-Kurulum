# Arch Linux Kurulum Rehberi

Merhabalar, bu oluşturmuş olduğum markdown dosyası benim Arch Linux kurarken izlediğim adımları içeriyor.


# İlk Ayarlar

Arch iso yazdığımız USB'yi boot ettikten sonra yapmamız gereken ilk ayar;

    loadkeys trq
ile klavyemizi Türkçeleştirmek.

Sonrasında `wifi-menu` komutu ile bir ağa bağlanmamız gerekiyor. Kablo ile bağlıysanız yapmanıza gerek yok.

## Başlangıç

 `cgdisk /dev/sda` komutu ile diskinizi açın.
 
 İlk olarak **EFI Partiton** kısmını oluşturacağız
 

    Size of Sectors or KMGTP  	-> 1G
    type						-> ef00
 Free space den yapacağımız ilk oluşturmada yukarıdaki gibi 1 Gigabyte'lık ve type'ı ef00 olan bir **EFI Partition** oluşturduk.

Swap Area yapmayacaksak kalan kısmın tamamını **Linux Filesystem** olarak yani Enter-Enter diye geçerek oluşturabiliriz.

Alanlarınızı oluşturduktan sonra *Write* seçeneğini seçmezseniz değişiklikleriniz işlenmez.

**Oluşturduğunuz EFI ve Linux Filesystem bölümlerinin hangilerinin /dev/sda1 hangilerinin /dev/sda2 olduğunu kağıda not edin. Her disk bölümü farklı bir numara alırlar.**
## Oluşturduğumuz Alanları Biçimlendirme

Oluşturduğumuz **EFI Partition** kısmını;

    mkfs.fat -F32 /dev/sdaX
komutu ile FAT32 formatında biçimlendiriyoruz.

Oluşturduğumuz **Linux Filesystem** kısmını;

    mkfs.ext4 /dev/sdaX
komutu ile ext4 formatında biçimlendiriyoruz.


## Partitionları Mount Etmek

Kurulumdan önce oluşturduğumuz partitionları mount etmemiz gerekiyor.
**Linux Filesystem**'i   */mnt* altına mount edeceğiz.

    mount /dev/sdaX /mnt
komutu ile /mnt altına mount ettik.

**EFI Partition** kısmını da /mnt/boot dizini oluşturup mount edeceğiz.

    mkdir /mnt/boot
komutu ile dizini oluşturduk.

    mount /dev/sdaX /mnt/boot

komutu ile de mount işlemini gerçekleştirdik.

## Kurulum

Sistemin temel parçalarını kuruyoruz.

    pacstrap /mnt base base-devel linux linux-firmware

Genfstab

    genfstab /mnt >> /mnt/etc/fstab

arch-chroot 'a geçiyoruz.

    arch-chroot /mnt

Aşağıdaki komut ile kuracağımız paketleri kuruyoruz.

    pacman -S xorg xorg-xinit sddm cinnamon dialog wpa_supplicant sudo gdm python3 xfce4-terminal opera firefox git code nano npm nodejs nomacs nmap mpv ntfs-3g unzip youtube-dl vi vim discord
Systemctl ayarlarını yapıyoruz.

    systemctl enable NetworkManager
    systemctl enable gdm

## Kullanıcı Eklemek

Aşağıdaki komut ile kullanıcı ekleyebilirsiniz. *username* kısmına istediğiniz kullanıcı adını girin.

    useradd -m username

Seçtiğiniz kullanıcı adına yeni bir parola belirleyin.

    passwd username

Aşağıdaki komutu girip /etc/sudoers dosyasını açın

    nano /etc/sudoers
`root ALL=(ALL) ALL` satırını bulup bir altına `username ALL=(ALL) ALL` satırını ekleyin ve Ctrl + X kombinasyonu ile kaydedin ve çıkın.

## Son Komutlar
Yazmanız gereken son komutlar;

    pacman -S grub efibootmgr os-prober

Çekirdeğimizin derlenmesine yarayan komut;

    mkinitcpio -p linux

Son olarak;

    grub-install --target=x86_64-efi --efi-directory=/boot /dev/sda

grub.cfg dosyamızı oluşturmak için.

    grub-mkconfig -o /boot/grub/grub.cfg


# Alabileceğiniz hatalar
Alabileceğiniz hataların çözümünü buraya gün geçtikçe ekleyeceğim. Bu hatalar yalnız Arch Linux kurulumunda değil genel olarak kullanımında da alacağınız hatalar olabilirler.
## Keyring Hatası
Kurulum yaparken keyring hatası alabilirsiniz. Bu hatayı çözmek için;

    nano /etc/pacman.conf
komutu ile paket yöneticimizin konfigürasyon dosyasını açın.
Açılan pencerede *SigLevel  = Required DatabaseOptional* satırını bulup başına *#* işareti koyarak yorum satırı haline getirin ve altına `SigLevel  = Never` satırını ekleyin. Görünüm son olarak buna benzeyecektir;

    #SigLevel  = Required DatabaseOptional
    SigLevel  = Never
yazarken harfi harfine ( boşluklar dahil ) aynı yazmaya dikkat ediniz.

sonrasında `sudo pacman -Sy archlinux-keyring` komutunu çalıştırın.
Böylece keyring hatasını çözmüş olacaksınız.

*Bu hatanın çözümünü UbuntuTR forumunda gösteren mhmtkrktr isimli kullanıcıya sevgiler.*

