## Execution

### İlk açılan terminal(Containerı başlatma terminali)

1.  **Klasöre gidin**
    ```bash
    cd robotaksi_ws
    ```

2.  **Konteyneri Başlatın:**

    ```bash
    docker-compose up
    ```
    
## Yeni terminal

1. **Konteynere Bağlanın:**
    Yeni bir terminal açın:
    ```bash
    docker exec -it robotaksi bash
    ```
    
2.
    ```bash
    cd ~/robotaksi_ws
    source install/setup.bash
    ```
    
## 2. Simülasyonu Başlatma (Ana Yöntem)

Tüm sistemi tek bir komutla başlatmak için `robotaksi_bringup` paketi kullanılır. Bu komut; Gazebo'yu, robot modelini, kontrolcüleri, lokalizasyonu (AMCL, EKF) ve Nav2 yığınını çalıştırır.

1.  **Terminali Hazırlayın:**
    Yeni bir terminal açın ve çalışma alanınızın kurulum betiğini `source` edin:
    
    ```bash
    docker exec -it robotaksi bash
    ```

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
