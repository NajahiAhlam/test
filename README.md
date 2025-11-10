@Service
@RequiredArgsConstructor
public class AcheteurExcelService {

    private final ArticleRepository articleRepository;
    private final FournisseurRepository fournisseurRepository;
    private final PriceArticleRepository priceArticleRepository;

    public void importFournisseurs(MultipartFile file) {
        try (Workbook workbook = new XSSFWorkbook(file.getInputStream())) {
            Sheet sheet = workbook.getSheetAt(0);
            Row headerRow = sheet.getRow(0);

            if (headerRow == null) {
                throw new RuntimeException("Le fichier Excel ne contient pas d'entête.");
            }

            // From column index 5 onward \u2192 fournisseur headers
            int fournisseurStartIndex = 5;
            List<String> fournisseursHeaders = new ArrayList<>();

            for (int i = fournisseurStartIndex; i < headerRow.getLastCellNum(); i++) {
                String raisonSocial = getCellValue(headerRow.getCell(i)).trim();
                if (!raisonSocial.isEmpty()) {
                    fournisseursHeaders.add(raisonSocial);
                }
            }

            for (int i = 1; i <= sheet.getLastRowNum(); i++) {
                Row row = sheet.getRow(i);
                if (row == null) continue;

                String code = getCellValue(row.getCell(0)).trim();
                if (code.isEmpty()) continue;

                Article article = articleRepository.findByCode(code)
                        .orElseThrow(() -> new RuntimeException("Article non trouvé: " + code));

                // Loop through fournisseurs
                for (int j = 0; j < fournisseursHeaders.size(); j++) {
                    String raisonSocial = fournisseursHeaders.get(j);
                    int cellIndex = fournisseurStartIndex + j;

                    String cellVal = getCellValue(row.getCell(cellIndex));
                    if (cellVal.isEmpty()) continue;

                    double prix = Double.parseDouble(cellVal);

                    // find or create fournisseur
                    Fournisseur fournisseur = fournisseurRepository.findByRaisonSocialIgnoreCase(raisonSocial)
                            .orElseGet(() -> {
                                Fournisseur f = new Fournisseur();
                                f.setRaisonSocial(raisonSocial);
                                return fournisseurRepository.save(f);
                            });

                    // check if priceArticle already exists
                    Optional<PriceArticle> existing = priceArticleRepository
                            .findByArticleAndFournisseur(article, fournisseur);

                    if (existing.isPresent()) {
                        existing.get().setPrice(prix);
                        priceArticleRepository.save(existing.get());
                    } else {
                        PriceArticle pa = new PriceArticle();
                        pa.setArticle(article);
                        pa.setFournisseur(fournisseur);
                        pa.setPrice(prix);
                        priceArticleRepository.save(pa);
                    }
                }
            }

        } catch (IOException e) {
            throw new RuntimeException("Erreur de lecture du fichier Excel: " + e.getMessage(), e);
        }
    }

    private String getCellValue(Cell cell) {
        if (cell == null) return "";
        if (cell.getCellType() == CellType.STRING) return cell.getStringCellValue();
        if (cell.getCellType() == CellType.NUMERIC) return String.valueOf(cell.getNumericCellValue());
        return "";
    }
}
