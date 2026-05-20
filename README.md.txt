graph TD
    %% Trafik Akisi
    Users((Kullanicilar)) -->|Internet| ExtIP[External IP Address]
    ExtIP --> CLB[Cloud Load Balancing]
    CLB -->|Port 80| SVC[Service: eczane-loadbalancer]
    
    subgraph eczane-cluster [GKE Cluster: eczane-cluster]
        SVC --> P1(Pod 1: eczane-frontend)
        SVC --> P2(Pod 2: eczane-frontend)
        SVC --> P3(Pod 3: eczane-frontend)
        
        %% Veritabani Baglantisi
        P1 -->|Port 3306 / NetworkPolicy| DB_SVC[Service: eczane-db-service]
        P2 -->|Port 3306 / NetworkPolicy| DB_SVC
        P3 -->|Port 3306 / NetworkPolicy| DB_SVC
        
        DB_SVC --> DB[(StatefulSet: eczane-mysql)]
        DB --> PVC[Kalıcı Disk - PVC]
    end
    
    %% CI/CD Akisi
    GitHub((GitHub Reposu)) -->|Push Tetikleyici| CB[Cloud Build Pipeline]
    CB -->|1. Build & Push| AR[Artifact Registry]
    CB -->|2. kubectl apply & rollout| eczane-cluster