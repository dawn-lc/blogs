```
var DB;
var DBName = '';
var TableName = '';

async function openDatabase() {
    return new Promise((resolve, reject) => {
        const request = indexedDB.open(DBName);

        request.onsuccess = (event) => {
            DB = event.target.result;
            console.log(`${DBName} 数据库已打开。`);
            resolve(DB);
        };

        request.onerror = (event) => {
            console.error(`无法打开数据库 ${DBName}: `, event.target.error);
            reject(event.target.error);
        };
    });
}

async function input(db, data) {
    let success = 0;
    let error = 0;
    for (let i = 0; i < data.length; i++) {
        const transaction = db.transaction(TableName, 'readwrite');
        const objectStore = transaction.objectStore(TableName);
        try {
            await addData(objectStore, data[i]);
            success++;
        } catch (err) {
            error++;
            console.error(`插入第 ${i + 1} 条数据失败。`, err);
        }
    }
    return { success, error };
}

function addData(objectStore, item) {
    return new Promise((resolve, reject) => {
        const request = objectStore.add(item);
        request.onsuccess = () => resolve();
        request.onerror = (event) => reject(event.target.error);
    });
}

async function output(db) {
    try {
        const data = await getAllData(db);
        const blob = new Blob([JSON.stringify(data)], { type: 'application/json' });
        const a = document.createElement('a');
        a.href = URL.createObjectURL(blob);
        a.download = 'indexedDB_Output.json';
        document.body.appendChild(a);
        a.click();
        document.body.removeChild(a);
        console.log("数据成功导出！");
    } catch (err) {
        console.error("导出数据失败：", err);
    }
}

function getAllData(db) {
    return new Promise((resolve, reject) => {
        const transaction = db.transaction(TableName, 'readonly');
        const objectStore = transaction.objectStore(TableName);
        const request = objectStore.getAll();
        request.onsuccess = (event) => resolve(event.target.result);
        request.onerror = (event) => reject(event.target.error);
    });
}

try {
    await openDatabase();
    console.log("数据库初始化完成，可以进行后续操作");
} catch (error) {
    console.error("数据库初始化失败", error);
}

```
使用方法：
```
let data;

//查询
data = await getAllData(DB);
console.log(data);

//导入
data = JSON.parse(fileData);
let result = await input(DB, data);
console.log(`导入完成。成功：${result.success} 条，失败：${result.error} 条。`);

//导出
output(DB);
```
