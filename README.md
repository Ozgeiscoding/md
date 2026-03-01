# 1.5 Adana Otonom - Robotaksi Çalışma Alanı (Workspace)

Bu depo, 1.5 Adana Otonom takımının TEKNOFEST Robotaksi yarışması için geliştirdiği ROS 2 tabanlı otonom araç yazılımını içerir.

## Sistem Mimarisi ve Hedefler


- **Mevcut Aşama:** Aşama 2: Temel Otonomi
- **Mevcut Hedef:** Simülasyon ortamında haritası çıkarılmış bir parkurda şerit takibi ve temel hedef takibi görevlerini başarıyla tamamlamak.

## 1. Gereksinimler

Geliştirme ortamınızın aşağıdaki gereksinimleri karşıladığından emin olun:

-   **İşletim Sistemi:** Ubuntu 22.04 LTS
-   **ROS 2 Sürümü:** ROS 2 Humble Hawksbill (Desktop Install)
-   **Simülasyon:** Gazebo Fortress (ROS 2 ile birlikte gelir)
-   **Derleme Aracı:** `colcon`
-   **Bağımlılık Yöneticisi:** `rosdep`
-   **Versiyon Kontrol:** `git`

## 2. Kurulum ve Derleme

Sistemi çalıştırmak için aşağıdaki adımları izleyin:

1.  **Çalışma Alanını (Workspace) Oluşturun:**
    ```bash
    mkdir -p ~/robotaksi_ws/src
    cd ~/robotaksi_ws
    ```

2.  **Depoyu Klonlayın:**
    ```bash
    git clone https://github.com/1-5-Adana-SGM-Otonom/robotaksi_ws.git
    ```

3.  **ROS 2 Bağımlılıklarını Yükleyin:**
    `rosdep`, `package.xml` dosyalarını tarayarak projenin ihtiyaç duyduğu tüm sistem ve ROS paketlerini otomatik olarak kurar. Bu adımı atlamayın.
    ```bash
    sudo rosdep init
    rosdep update
    # rosdep install -i --from-path src -y --rosdistro humble
    # Alternatif olarak, sağlanan kurulum betiğini kullanabilirsiniz:
    ./setup_dependencies.sh
    ```

    **Python Bağımlılıkları:**
    Proje için gerekli Python kütüphanelerini yüklemek için:
    ```bash
    pip3 install -r requirements.txt
    ```

4.  **Çalışma Alanını Derleyin:**
    `colcon build`, `src` klasöründeki tüm paketleri derler. `--symlink-install` parametresi, Python veya launch dosyalarını her değiştirdiğinizde tekrar derleme yapma ihtiyacını ortadan kaldırır.
    ```bash
    colcon build --symlink-install
    ```

## 2.1 Docker ile Kurulum (Önerilen)

Bağımlılık sorunlarını ortadan kaldırmak ve tüm platformlarda tutarlı bir ortam sağlamak için Docker kullanabilirsiniz.

### Gereksinimler
- Docker ve Docker Compose
- (Linux/Windows) NVIDIA GPU + [NVIDIA Container Toolkit](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/latest/install-guide.html)
- (macOS) XQuartz (`brew install --cask xquartz`)

### Kurulum

1.  **Depoyu Klonlayın:**
    ```bash
    git clone https://github.com/1-5-Adana-SGM-Otonom/robotaksi_ws.git
    cd robotaksi_ws
    ```

2.  **Docker İmajını Derleyin:**
    ```bash
    docker-compose build
    ```

3.  **Konteyneri Başlatın:**

    **Linux/Windows (GPU):**
    ```bash
    xhost +local:docker  # X11 erişimi için
    docker-compose up
    ```

    **macOS:**
    ```bash
    xhost +localhost
    docker-compose -f docker-compose-mac.yml up
    ```

4.  **Konteynere Bağlanın:**
    Yeni bir terminal açın:
    ```bash
    docker exec -it robotaksi bash
    ```

### Platform Uyumluluğu

| Platform | GPU Desteği | Notlar |
|---|---|---|
| Linux (NVIDIA) | ✅ Tam | En iyi performans |
| Windows (WSL 2 + NVIDIA) | ✅ Tam | WSLg ile GUI otomatik çalışır |
| macOS (Intel/Apple Silicon) | ❌ CPU | `docker-compose-mac.yml` kullanın, simülasyon yavaş olabilir |

### Sorun Giderme: NVIDIA GPU Hatası

`docker-compose up` çalıştırırken aşağıdaki hatayı alıyorsanız:

```
nvidia-container-cli: initialization error: load library failed: 
libnvidia-ml.so.1: cannot open shared object file: no such file or directory
```

**Sebep:** Docker, NVIDIA sürücü kütüphanelerine erişemiyor. Bu genellikle sürücü eksikliği, kernel modüllerinin yüklenmemesi veya Docker'ın güncel olmayan konfigürasyonundan kaynaklanır.

**Çözüm:**

1.  **NVIDIA Container Toolkit'i kurun:**
    ```bash
    # Toolkit deposunu ekleyin
    curl -fsSL https://nvidia.github.io/libnvidia-container/gpgkey | sudo gpg --dearmor -o /usr/share/keyrings/nvidia-container-toolkit-keyring.gpg
    curl -s -L https://nvidia.github.io/libnvidia-container/stable/deb/nvidia-container-toolkit.list | \
      sed 's#deb https://#deb [signed-by=/usr/share/keyrings/nvidia-container-toolkit-keyring.gpg] https://#g' | \
      sudo tee /etc/apt/sources.list.d/nvidia-container-toolkit.list
    
    # Toolkit'i kurun
    sudo apt-get update && sudo apt-get install -y nvidia-container-toolkit
    ```

2.  **Docker'ı yeniden kurun ve yapılandırın:**
    ```bash
    sudo nvidia-ctk runtime configure --runtime=docker
    sudo apt-get install --reinstall docker-ce
    sudo systemctl restart docker
    ```
    > ⚠️ `docker-ce` yeniden kurulumu kritik bir adımdır. Sürücü güncellemesi sonrası Docker eski konfigürasyonda kalabilir.

3.  **CDI modunu etkinleştirin** (opsiyonel, hala hata alıyorsanız):
    ```bash
    sudo nvidia-ctk cdi generate --output=/etc/cdi/nvidia.yaml
    sudo nano /etc/nvidia-container-runtime/config.toml
    # mode = "auto" satırını mode = "cdi" olarak değiştirin
    sudo systemctl restart docker
    ```

## 3. Simülasyonu Başlatma (Ana Yöntem)

Tüm sistemi tek bir komutla başlatmak için `robotaksi_bringup` paketi kullanılır. Bu komut; Gazebo'yu, robot modelini, kontrolcüleri, lokalizasyonu (AMCL, EKF) ve Nav2 yığınını çalıştırır.

1.  **Terminali Hazırlayın:**
    Yeni bir terminal açın ve çalışma alanınızın kurulum betiğini `source` edin:
    ```bash
    cd ~/robotaksi_ws
    source install/setup.bash
    ```

2.  **Ana Başlatma Komutunu Çalıştırın:**
    ```bash
    ros2 launch robotaksi_bringup robotaksi_bringup.launch.py
    ```
    Bu komut sonrası simülasyon ortamı (Gazebo) ve görselleştirme aracı (RViz) otomatik olarak açılmalıdır. Sistem otonom görevlere hazırdır.

## 4. Yardımcı Komutlar (Hata Ayıklama ve Özel Görevler İçin)

Ana başlatma komutu yerine, sistemi parça parça test etmek veya özel görevler (haritalama gibi) yapmak için aşağıdaki komutları kullanabilirsiniz.

-   **Sadece Manuel Sürüş (Joystick ile):**
    Aracın temel hareket kabiliyetlerini test etmek için bu komutu kullanabilirsiniz. Navigasyon yığını başlatılmaz.
    ```bash
    # Önce Gazebo'yu başlatın
    ros2 launch robotaksi_description gazebo.launch.py
    # Ardından joystick'i başlatın
    ros2 launch robotaksi_controller joystick.launch.py
    ```

-   **Haritalama (SLAM):**
    Yeni bir ortamın haritasını çıkarmak için bu komutu kullanın. Bu komut, AMCL yerine `slam_toolbox`'ı çalıştırır.
    ```bash
    ros2 launch robotaksi_mapping slam.launch.py
    ```

---
