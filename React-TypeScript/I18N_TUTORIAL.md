# 🌍 Guide Complet i18next : React + TypeScript

Ce guide récapitule les étapes d'installation et de configuration de i18next pour un projet professionnel.

## 📦 1. Installation des dépendances

Lancer dans le terminal :
`npm install i18next react-i18next i18next-http-backend i18next-browser-languagedetector`

---

## 📂 2. Fichiers de Traduction (Dossier Public)

Les fichiers doivent être dans `public/locales/`.

### Exemple de fichier : `public/locales/fr/translation.json`

{
"newsletter": {
"title": "Restez informé",
"subtitle": "Recevez nos dernières actualités tech chaque semaine.",
"input_placeholder": "Votre adresse email...",
"button_submit": "S'abonner",
"current_lang": "Langue actuelle",
"switch_lang_link": "Passer en Anglais"
}
}

### Exemple de fichier : `public/locales/en/translation.json`

{
"newsletter": {
"title": "Stay informed",
"subtitle": "Receive our latest tech news every week.",
"input_placeholder": "Your email address...",
"button_submit": "Subscribe",
"current_lang": "Current language",
"switch_lang_link": "Switch to French"
}
}

---

## ⚙️ 3. Configuration (`src/i18n.ts`)

```tsx
import i18n from "i18next";
import { initReactI18next } from "react-i18next";
import HttpApi from "i18next-http-backend";
import LanguageDetector from "i18next-browser-languagedetector";

i18n
  .use(HttpApi)
  .use(LanguageDetector)
  .use(initReactI18next)
  .init({
    fallbackLng: "fr",
    debug: false,
    interpolation: { escapeValue: false },
    backend: { loadPath: "/locales/{{lng}}/translation.json" },
  });

export default i18n;
```

---

## 🚀 4. Initialisation (`src/main.tsx`)

IMPORTANT : L'import de `./i18n` doit se faire en haut du fichier pour initialiser le traducteur avant que React ne commence à monter les composants.

```tsx
import "./i18n";
import { Suspense } from "react";
import App from "./App";

// Pourquoi Suspense ?
// Parce que i18next charge les fichiers JSON via une requête réseau.
// Suspense permet d'attendre que les fichiers de langue soient téléchargés
// avant d'afficher l'application, évitant ainsi d'afficher des clés vides.

<Suspense fallback={<LoadingTemplate />}>
  <App />
</Suspense>;
```

---

## 🔘 5. Composant atome `ButtonLanguage` de changement de langue

Un composant atome réutilisable pour basculer entre les langues.

```tsx
export interface ButtonLanguageProps {
  label: string;
  isActive: boolean;
  onClick: () => void;
}

export const ButtonLanguage = ({
  label,
  isActive,
  onClick,
}: ButtonLanguageProps) => {
  return (
    <button
      onClick={onClick}
      className={`btn btn-sm ${isActive ? "btn-primary" : "btn-outline-secondary"} m-1`}
      style={{ fontWeight: isActive ? "bold" : "normal" }}
    >
      {label}
    </button>
  );
};
```

---

## 🔘 6. Composant molécule `LanguageSelector` de changement de langue

```tsx
import { useTranslation } from "react-i18next";
import { ButtonLanguage } from "ton chemin";

export const LanguageSelector = () => {
  const { i18n } = useTranslation();

  const languages = [
    { code: "fr", label: "FR" },
    { code: "en", label: "EN" },
  ];

  return (
    <div className="language-selector d-flex align-items-center">
      {languages.map((lang) => (
        <ButtonLanguage
          key={lang.code}
          label={lang.label}
          isActive={i18n.language === lang.code}
          onClick={() => i18n.changeLanguage(lang.code)}
        />
      ))}
    </div>
  );
};
```

---

## 🧭 7. Intégration

Pour rendre le changement de langue accessible partout, on utilise la molécule à l'intérieur de notre organisme de navigation (ou autre).

```tsx
import { LanguageSelector } from "ton chemin";

export const Navbar = () => {
  return (
    <nav className="flex justify-between items-center p-4 bg-white shadow-sm">
      {/* Logo ou Titre */}
      <div className="font-bold text-xl">MyProject</div>
      {/* Menu de navigation principal */}
      <NavMenu />
      {/* Boutons de changement de langue */}
      <div className="ml-4">
        <LanguageSelector />
      </div>
    </nav>
  );
};
```

---

## 📝 Exemple d'utilisation dans un composant (`Newsletter.tsx`)

Cet exemple montre comment utiliser le hook `useTranslation` pour traduire du texte, des variables et des attributs HTML.

```tsx
import { useTranslation } from "react-i18next";

export const Newsletter = () => {
  // t : fonction de traduction
  const { t } = useTranslation();

  return (
    <section className="p-8 border rounded-lg bg-white shadow">
      <h2 className="text-2xl font-bold mb-4">{t("newsletter.title")}</h2>
      <p className="mb-6 text-gray-600">{t("newsletter.subtitle")}</p>

      <div className="flex flex-col gap-4">
        <input
          type="email"
          placeholder={t("newsletter.input_placeholder")}
          className="border p-2 rounded"
        />
        <button className="bg-blue-600 text-white px-4 py-2 rounded">
          {t("newsletter.button_submit")}
        </button>
      </div>
    </section>
  );
};
```

---

## 🛠 5. Debug & Cache

1. Si les textes ne changent pas : faire CTRL + F5.
2. Si erreur 504 : `rm -rf node_modules/.vite` et relancer.
3. Vérifiez bien que vos fichiers sont dans /public/ et pas /src/.
