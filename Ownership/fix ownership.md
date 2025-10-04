This error is telling you that the **build process doesn’t have permission to delete (unlink) a file** inside your project’s `dist` folder:

```
EACCES: permission denied, unlink '/home/moniesaver/DailyLogApp/dist/assets/index-w_Cm0CFt.js'
```

---

### 🔎 Breaking it down

* **`EACCES: permission denied`** → Linux blocked the operation due to file permissions.
* **`unlink`** → means "delete a file" in Unix terminology.
* **`/home/moniesaver/DailyLogApp/dist/assets/...`** → the build process is trying to remove old build files before writing new ones.

---

### ⚡ Why it happens

1. **Wrong ownership/permissions**

   * Maybe you previously built as `root` or another user, so some files in `dist/` are owned by `root`.
   * Now your current user (`moniesaver`) doesn’t have permission to remove them.

2. **Running inside Yarn Plug’n’Play (PnP)**

   * The `.pnp.cjs` in the trace shows you’re using Yarn PnP, but the root cause is still **Linux file permissions**.

---

### ✅ How to fix

1. **Check file ownership**

   ```bash
   ls -la /home/moniesaver/DailyLogApp/dist/assets/
   ```

   If you see files owned by `root`, that’s the problem.

2. **Fix ownership** (make sure your normal user owns the project folder)

   ```bash
   sudo chown -R moniesaver:moniesaver /home/moniesaver/DailyLogApp
   ```

3. **Clean `dist` manually**

   ```bash
   rm -rf /home/moniesaver/DailyLogApp/dist
   ```

   Then rebuild.

4. **Avoid using `sudo` when running builds**

   * Always run `yarn build` or `npm run build` as your normal user, not `root`.

---

### 🔑 Summary

Your build process is failing because it **can’t delete an old build artifact** due to ownership/permission mismatch.
Fix it by **resetting ownership of your project files** to your user and rerun the build.

👉 Do you want me to give you a **safe one-liner that resets all permissions for your project folder** (without breaking other system files)?
