

# 【问题1】

```
  File "F:\github_python\assignment2-systems\.venv\Lib\site-packages\torch\distributed\rendezvous.py", line 194, in _create_c10d_store
    return TCPStore(
           ^^^^^^^^^
RuntimeError: use_libuv was requested but PyTorch was build without libuv support
```

Why is this happening?

1.  **Windows Limitations:** PyTorch's distributed support (`torch.distributed`) is primarily designed for Linux. Windows support is considered experimental.
2.  **TCPStore:** When `init_process_group` is called with the "gloo" backend, it creates a `TCPStore` for process rendezvous.
3.  **LibUV:** Recent versions of PyTorch try to use `libuv` for better performance on Windows, but if the binary wasn't compiled with it, it crashes. Setting `USE_LIBUV=0` forces PyTorch to fall back to standard Winsock sockets, which works reliably for basic assignments.


# 【问题2】colab


This error (`ProcessExitedException: process 1 terminated with exit code 1`) is generic---it tells you a child process crashed, but hides the actual underlying error. Based on your traceback (`/tmp/ipython-input...`), you are running this code inside a **Jupyter Notebook or Google Colab**.

### ⚠️ The Core Problem

**`torch.multiprocessing.spawn` does not work reliably in Jupyter Notebooks.**

Notebooks do not handle the `if __name__ == "__main__":` guard correctly, and the interactive kernel environment conflicts with Python's `spawn` multiprocessing method. This causes child processes to fail silently or crash immediately.