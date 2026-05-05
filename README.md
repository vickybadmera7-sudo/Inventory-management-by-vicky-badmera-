# Inventory-management-by-vicky-badmera-
Inventory management for oop
# C / C++ Inventory Manager

A console-based CRUD inventory manager that combines a **C data layer** (binary file storage via `fread`/`fwrite`/`fseek`) with a **C++ UI layer** (classes, `std::vector`, `std::sort`).

---

## Project Structure

```
inventory_project/
├── include/
│   ├── inventory.h          # Item struct + C backend API (extern "C")
│   └── InventoryManager.h   # C++ class declaration
├── src/
│   ├── inventory.c          # C backend: binary file storage
│   ├── InventoryManager.cpp # C++ class wrapping C functions
│   └── main.cpp             # Menu loop
├── Makefile
├── CMakeLists.txt
└── README.md
```

---

## Build & Run

### Using Make (recommended)

```bash
# Build
make

# Run
./inventory

# Build and run in one step
make run

# Remove build artefacts and data file
make clean
```

### Using CMake

```bash
mkdir build && cd build
cmake ..
make
./inventory
```

> `inventory.dat` is created in the directory you run the executable from.  
> Delete it with `make clean` (Make) or `rm inventory.dat` (CMake) to reset.

---

## Menu

```
1) Add item
2) View item by ID
3) Update item
4) Delete item
5) List all items (sorted by ID)
6) List all items (sorted by name)
7) Exit
```

---

## Design Notes

| Layer | File(s) | Key techniques |
|-------|---------|----------------|
| C data backend | `inventory.c` | `fread`/`fwrite`/`fseek`, soft-delete via `is_deleted` flag |
| C++ UI / logic | `InventoryManager.cpp` | Class, `std::vector<Item>`, `std::sort` with lambda |
| Header bridge | `inventory.h` | `extern "C"` block so C++ can link against C symbols |

---

## Test Cases

All five tests were run sequentially in one session to verify persistence across restarts.

- **TC-1 – Add & persist**  
  Added items with IDs 1 (`Widget`, qty 10, $4.99), 2 (`Gadget`, qty 5, $19.99), 3 (`Doohickey`, qty 0, $0.50).  
  Exited, re-launched, selected *List all (by ID)* → all three items appeared correctly. ✅

- **TC-2 – Duplicate ID rejected**  
  Attempted to add another item with ID 1 while ID 1 already existed.  
  App printed `[!] Failed: ID 1 already exists.` and did not corrupt the file. ✅

- **TC-3 – Update persists across restart**  
  Updated item 2 (`Gadget`) to qty 50 and price $24.99.  
  Exited, re-launched, selected *View item* (ID 2) → new values shown. ✅

- **TC-4 – Soft delete hides item**  
  Deleted item 3 (`Doohickey`).  
  *List all* showed only IDs 1 and 2.  
  *View item* for ID 3 returned `[!] Item 3 not found (or deleted)`. ✅

- **TC-5 – Input validation**  
  Entered `-5` for ID → app re-asked.  
  Entered empty string for name → app re-asked.  
  Entered `-1` for quantity → app re-asked.  
  Entered `abc` for price → app re-asked. No crash in any case. ✅
