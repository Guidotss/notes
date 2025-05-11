# 📂 Repositorios bare (bare repos)

## 🧠 ¿Qué es un repo bare?

Un **repositorio bare** es un tipo especial de repositorio Git que no tiene archivos de trabajo. Solo contiene los datos internos de Git (commits, objetos, refs).

Se usa exclusivamente como **repositorio remoto**, es decir, para recibir `git push` y permitir `git clone`.

---

## 📁 Estructura típica

Cuando ejecutás `git init --bare`, se crea una carpeta como esta:

mi-repo.git/ ├── HEAD ├── config ├── objects/ ├── refs/ └── hooks/

yaml
Copiar
Editar

No tiene archivos del proyecto (como `README.md`, `index.js`, etc.).

---

## ✅ ¿Para qué se usa?

- Como servidor remoto de Git
- Para recibir y almacenar los cambios (`git push`)
- Para ser clonado por otros usuarios (`git clone`)

GitHub, GitLab y Bitbucket internamente usan repos **bare**.

---

## 🛠 ¿Cómo crear un repo bare?

```bash
git init --bare /data/repos/usuario/mi-repo.git
Podés crear la estructura completa con:

bash
Copiar
Editar
mkdir -p /data/repos/nico
cd /data/repos/nico
git init --bare mi-repo.git
📦 Dónde se guardan
Se recomienda una estructura por usuario:

bash
Copiar
Editar
/data/repos/
├── guido/
│   ├── repo1.git/
│   └── otro-repo.git/
├── nico/
│   └── nico-repo.git/
```


| Característica         | Repo normal (`git init`) | Repo bare (`git init --bare`) |
|------------------------|--------------------------|-------------------------------|
| Tiene archivos         | ✅                        | ❌                            |
| Tiene carpeta `.git/`  | ✅ (oculta en el root)    | ❌ (es la raíz del repo)      |
| Se puede editar código | ✅                        | ❌                            |
| Sirve para `push`      | ✅                        | ✅                            |
| Sirve para `clone`     | ✅                        | ✅                            |
| Sirve para `pull`      | ✅                        | ❌                            |
| Ideal para desarrollo  | ✅                        | ❌                            |
| Ideal como servidor    | ❌                        | ✅                            |
