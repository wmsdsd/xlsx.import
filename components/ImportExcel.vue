<template>
    <div class="layout">
        <input type="file" text="불러오기" @change="readExcelFile" />
        <div class="excel">
            <div class="excel-sheet">
                <div class="excel-sheet-tab" v-for="name in sheetNameList">{{ name }}</div>
            </div>
            <div class="excel-data">
                <table>
                    <thead>
                    <tr>
                        <th></th>
                    </tr>
                    </thead>
                    <tbody>
                    <tr>
                        <td></td>
                    </tr>
                    </tbody>
                </table>
            </div>
        </div>
    </div>
</template>

<script>
const XLSX = require('xlsx')

export default {
    name: "ImportExcel",
    data() {
        return {
            fileReader: null,
            selectedIndex: null,
            sheetNameList: [],
            excelDataList: []
        }
    },
    methods: {
        readExcelFile(event) {

            let input = event.target
            let file = input.files[0]
            let fileName = file.name
            let extension = fileName.split('.')[1]
            let includeExtension = ['xlsx', 'csv', 'xls']

            if (!includeExtension.includes(extension)) {
                alert('엑셀 형식의 파일을 넣어주세요.')
                return
            }

            this.fileReader = new FileReader()
            this.fileReader.onload = this.onLoadFile
            this.fileReader.readAsBinaryString(file)

            event.target.value = ''
        },
        onLoadFile() {
            if (!this.fileReader) return

            let data = this.fileReader.result
            let workBook = XLSX.read(data, {type: 'binary'})

            // set sheet name
            this.sheetNameList = workBook.SheetNames

            // read sheet
            let rowDataList = []
            this.sheetNameList.forEach((name, index) => {
                let rows = XLSX.utils.sheet_to_json(workBook.Sheets[name])
                console.log(rows)

                rowDataList[index] = rows
            })

            this.excelDataList = rowDataList
            console.log(this.excelDataList)
        }
    }
}
</script>

<style scoped>

</style>
