Tabii! Senin verdiğin Markdown dosyasını hem okunabilirliği artıracak hem de adım numaralarını ve başlıkları daha tutarlı hâle getirecek şekilde düzenledim. Gereksiz tekrarları ve dağınıklığı da azalttım:

---

## Robotaksi Simülasyon Başlatma Rehberi

### 1. İlk Terminal (Container Başlatma)

1. **Çalışma klasörüne gidin:**

   ```bash
   cd robotaksi_ws
   ```

2. **Docker konteynerini başlatın:**

   ```bash
   docker-compose up
   ```

---

### 2. Yeni Terminal (Konteynere Bağlanma)

1. Yeni bir terminal açın ve konteynere bağlanın:

   ```bash
   docker exec -it robotaksi bash
   ```

2. Çalışma alanına gidin ve ROS 2 ortamını yükleyin:

   ```bash
   cd ~/robotaksi_ws
   source install/setup.bash
   ```

---

### 3. Simülasyonu Başlatma (Ana Yöntem)

`robotaksi_bringup` paketi ile tüm sistemi tek komutla başlatabilirsiniz. Bu komut; Gazebo, robot modeli, kontrolcüler, lokalizasyon (AMCL, EKF) ve Nav2 yığınını çalıştırır.

1. Yeni terminal açın, konteynere bağlanın ve ortamı yükleyin:

   ```bash
   docker exec -it robotaksi bash
   cd ~/robotaksi_ws
   source install/setup.bash
   ```

2. Ana başlatma komutunu çalıştırın:

   ```bash
   ros2 launch robotaksi_bringup robotaksi_bringup.launch.py
   ```

   Bu komut ile Gazebo ve RViz açılır; sistem otonom görevler için hazır hâle gelir.

---

### 4. Yardımcı Komutlar (Hata Ayıklama ve Özel Görevler)

* **Sadece Manuel Sürüş (Joystick ile):**
  Aracın temel hareketlerini test etmek için:

  ```bash
  # Gazebo'yu başlatın
  ros2 launch robotaksi_description gazebo.launch.py
  # Ardından joystick'i başlatın
  ros2 launch robotaksi_controller joystick.launch.py
  ```

* **Haritalama (SLAM):**
  Yeni bir ortamın haritasını çıkarmak için:

  ```bash
  ros2 launch robotaksi_mapping slam.launch.py
  ```

---

İstersen ben bunu ayrıca **daha görselleştirilmiş ve renkli kod bloklarıyla, adımları ikonlarla zenginleştirilmiş bir versiyon** hâline de getirebilirim; böylece rehber çok daha okunaklı ve takip etmesi kolay olur.

Bunu yapmamı ister misin?
