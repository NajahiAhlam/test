package com.example.cockpit.utils;

import com.example.cockpit.entities.P2p;
import com.example.cockpit.entities.SciformaTimesheets;
import org.apache.poi.ss.usermodel.*;
import org.apache.poi.xssf.usermodel.XSSFWorkbook;
import org.springframework.web.multipart.MultipartFile;

import java.io.ByteArrayInputStream;
import java.io.ByteArrayOutputStream;
import java.io.IOException;
import java.io.InputStream;
import java.text.SimpleDateFormat;
import java.util.ArrayList;
import java.util.Iterator;
import java.util.List;

public class ExcelHelperP2p {
    public static final String TYPE = "application/vnd.openxmlformats-officedocument.spreadsheetml.sheet";
    static final String[] HEADERs = {
            "Programme", "Code_Sciforma", "Code_P2P", "Nom_Projet", "Rubrique", "Total_Engagement", "Annee_ENG"
    };
    static final String SHEET = "Sciforma";
    private static final SimpleDateFormat dateFormat = new SimpleDateFormat("yyyy-MM-dd");

    public static boolean hasExcelFormat(MultipartFile file) {
        return TYPE.equals(file.getContentType());
    }

    public static ByteArrayInputStream p2pToExcel(List<P2p> p2ps) {
        try (Workbook workbook = new XSSFWorkbook(); ByteArrayOutputStream out = new ByteArrayOutputStream()) {
            Sheet sheet = workbook.createSheet(SHEET);

            // Write the header row
            writeHeaderRow(sheet);

            // Write data rows
            writeDataRows(p2ps, sheet);

            // Write the workbook to the output stream
            workbook.write(out);
            return new ByteArrayInputStream(out.toByteArray());
        } catch (IOException e) {
            throw new RuntimeException("Failed to import data to Excel file: " + e.getMessage(), e);
        }
    }


    private static void writeDataRows(List<P2p> p2ps, Sheet sheet) {
        int rowIdx = 1;
        for (P2p p2p : p2ps) {
            Row row = sheet.createRow(rowIdx++);
            setCellValue(row, 0, p2p.getProgramme());
            setCellValue(row, 1, p2p.getCodeSciforma());
            setCellValue(row, 2, p2p.getCodeP2p());
            setCellValue(row, 3, p2p.getNomProjet());
            setCellValue(row, 4, p2p.getTotalEngegement());
            setCellValue(row, 5, p2p.getAnneeEng());

        }
    }


    private static void setCellValue(Row row, int colIndex, String value) {
        Cell cell = row.createCell(colIndex);
        if (value != null) {
            cell.setCellValue(value);
        } else {
            cell.setCellType(CellType.BLANK);


        }
    }

    private static void writeHeaderRow(Sheet sheet) {
        Row headerRow = sheet.createRow(0);
        for (int col = 0; col < HEADERs.length; col++) {
            Cell cell = headerRow.createCell(col);
            cell.setCellValue(HEADERs[col]);
        }
    }
    public static List<P2p> excelToP2p(InputStream is) {
        try (Workbook workbook = new XSSFWorkbook(is)) {
            Sheet sheet = workbook.getSheet(SHEET);
            Iterator<Row> rows = sheet.iterator();

            List<P2p> p2ps = new ArrayList<>();
            int rowNumber = 0;
            while (rows.hasNext()) {
                Row currentRow = rows.next();
                if (rowNumber == 0) {
                    rowNumber++;
                    continue;
                }
                P2p p2p = parseRow(currentRow);
                p2ps.add(p2p);
            }
            return p2ps;
        } catch (IOException e) {
            throw new RuntimeException("Failed to parse Excel file: " + e.getMessage(), e);
        }
    }

    private static P2p parseRow(Row currentRow) {
        P2p p2p = new P2p();
        Iterator<Cell> cellsInRow = currentRow.iterator();
        int cellIdx = 0;
        while (cellsInRow.hasNext()) {
            Cell currentCell = cellsInRow.next();
            setSciformaValue(p2p, currentCell, cellIdx);
            cellIdx++;
        }
        return p2p;
    }

    private static void setSciformaValue(P2p p2p, Cell cell, int cellIdx) {
        switch (cellIdx) {
            case 0:
                p2p.setProgramme(getCellValueAsString(cell));
                break;
            case 1:
                p2p.setCodeSciforma(getCellValueAsString(cell));
                break;
            case 2:
                p2p.setCodeP2p(getCellValueAsString(cell));
                break;
            case 3:
                p2p.setNomProjet(getCellValueAsString(cell));
                break;
            case 4:
                p2p.setTotalEngegement(getCellValueAsString(cell));
                break;
            case 5:
                p2p.setAnneeEng(getCellValueAsString(cell));
                break;
            default:
                break;
        }
    }

    private static String getCellValueAsString(Cell cell) {
        if (cell == null) {
            return "";
        }
        switch (cell.getCellType()) {
            case STRING:
                return cell.getStringCellValue();
            case NUMERIC:
                if (DateUtil.isCellDateFormatted(cell)) {
                    return dateFormat.format(cell.getDateCellValue());
                } else {
                    return String.format("%.0f", cell.getNumericCellValue());
                }
            case BOOLEAN:
                return Boolean.toString(cell.getBooleanCellValue());
            case FORMULA:
                return cell.getCellFormula();
            case BLANK:
                return "";
            default:
                return "Unsupported Cell Type";
        }
    }
}
