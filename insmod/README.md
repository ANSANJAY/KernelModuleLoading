# Kernel Module Loading

A kernel module is a piece of code that can be dynamically added or removed from the kernel, allowing the core functionality of the operating system to be extended `without a reboot`

-  When you execute `insmod` on a module, you are initiating the process of loading a kernel module into the running kernel.

Here's what happens in a bit more detail:

a) `init_module()`: This is called to inform the kernel that an attempt is being made to load a module, passing control over to the kernel.

b) `sys_init_module()`: Run within the kernel, this function performs several operations:
   - `Verifies the permissions` of the user attempting to load the module.
   - Calls `load_module` after verification which:
     - Assigns `temporary memory and transfers the ELF` (Executable and Linkable Format) module from user space to kernel memory with `copy_from_user`.
     - Checks the `sanity` of the ELF file ensuring it's a proper ELF file.
     - Generates convenience variables (offsets) in the temporary memory space based on the ELF file interpretation.
     - Copies user arguments to the module into kernel memory.
     - Performs symbol resolution.
     - Returns a reference to the kernel module.
   
c) The reference to the module is added to a doubly linked list containing all loaded modules in the system.

d) Finally, the `module_init` function in the module code is called to complete the loading process.

### 2. Curious Questions 🤔
**Q1:** What is the purpose of the `init_module()` function when performing `insmod` on a module?
**A1:** The `init_module()` function intimates the kernel that there is an attempt to load a module and it transfers control to the kernel to proceed with the loading process.

**Q2:** What is the role of `sys_init_module()` in the process of loading a kernel module?
**A2:** `sys_init_module()` is a kernel function that performs a series of operations including verifying user permissions, calling the `load_module` function to copy the module and user arguments to kernel memory, check ELF file sanity, perform symbol resolution, and add the module reference to the list of loaded modules in the system.

**Q3:** Why is it important to check the sanity of the ELF file during the `insmod` process?
**A3:** Checking the sanity of the ELF file is crucial to ensure that the file is a proper and valid Executable and Linkable Format file. This step ensures the integrity and reliability of the module being loaded, preventing the loading of corrupt or incompatible modules which could cause system instability or vulnerabilities.

### 3. Explain the concept in simple words 🌟
Think of a kernel module like a puzzle piece 🧩. The kernel is the main part of the operating system, and sometimes, it needs additional pieces to perform new tasks. When we use `insmod`, it's like we're trying to fit this new piece into our already existing puzzle (kernel).

a) **init_module() is like asking permission 🙋‍♂️.** 
   - It's like saying, "Hey, I have this new puzzle piece; can I add it here?"

b) **sys_init_module() is like checking and placing the piece 🕵️.**
   - It looks at who is trying to add the piece, making sure they are allowed to do so.
   - It carefully examines the new piece, ensuring it belongs to this puzzle, and then places it in the right spot, making sure it fits perfectly.

c) **Finally, adding to the list and calling module_init 📝.**
   - Once the piece is in, we make a note of it in our puzzle list, and we do any final adjustments needed to make sure it's settled in just right.

This whole process makes sure that our puzzle, the kernel, stays happy, secure, and able to do more cool stuff with the new pieces added! 🎉