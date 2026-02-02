# 🚀 GUIDE COMPLET : INSTALLATION & SÉCURISATION MYSQL SUR LINUX

## 1. INSTALLATION DES PAQUETS

```bash
sudo apt update
sudo apt install mysql-server -y
sudo systemctl start mysql
```

## 2. SÉCURISATION DU SERVEUR

# Lance la commande et réponds : N, TonMdp, Y, Y, Y, Y

```bash
sudo mysql_secure_installation
```

## 3. CONFIGURATION DU PLUGIN POUR EXPRESS (CRUCIAL)

# Connecte-toi d'abord

```bash
sudo mysql -u root -p
```

# Dans le prompt mysql, colle ces 3 lignes :

```bash
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'MDP';
FLUSH PRIVILEGES;
EXIT;
```
