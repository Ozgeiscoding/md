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
   source install/setup.bash
   ```

---

### 3. Simülasyonu Başlatma Terminali

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


### 4. Navigasyonu Ayrı Başlatmak (Opsiyonel)

Eğer bringup içinde navigation yoksa veya navigasyonu ayrı başlatmak istiyorsanız:

```bash
ros2 launch robotaksi_navigation navigation.launch.py
```

Bu komut Nav2 stack'ini başlatır (planner, controller, bt_navigator vb.).

---


