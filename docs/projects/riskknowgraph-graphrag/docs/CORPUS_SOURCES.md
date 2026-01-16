# CORPUS_SOURCES

目标

- 该清单用于记录 corpus 下每一篇语料的来源与许可信息
- repo 为 private, 允许 Git LFS, 语料文件允许 commit
- 语料覆盖中文与英文, 重点 FRTB 与 CVA, 同时包含其他衍生品风险主题
- 语料格式覆盖 pdf html docx markdown

约定

- corpus 内文件按相对路径作为 doc_id, 例如 `pdf/en/bis_bcbs_d457.pdf`
- 每条语料必须记录 license 或 permissions 条款链接
- 若来源条款限制 non commercial, 必须在 notes 中标注

语料条目模板

```yaml
- doc_id: pdf/en/example.pdf
  title: ""
  language: en
  topics:
    - frtb
    - cva
  format: pdf
  source_url: ""
  retrieved_at: ""
  license_name: ""
  license_url: ""
  permissions_excerpt: ""
  notes: ""
```

候选来源池

- BIS terms and conditions
  - url: <https://www.bis.org/terms_conditions.htm>
  - notes: non commercial redistribution and limited extract rules, see page for details

- FSB terms and conditions
  - url: <https://www.fsb.org/terms_conditions/>
  - notes: allows reproduce except for commercial purposes, must cite source, no modification

- UK Open Government Licence v3.0
  - url: <https://www.nationalarchives.gov.uk/doc/open-government-licence/version/3/>
  - notes: allows commercial reuse with attribution, useful for UK public sector material

种子语料清单 v0

```yaml
- doc_id: pdf/en/bis_bcbs_d457.pdf
  title: "Minimum capital requirements for market risk"
  language: en
  topics:
    - frtb
    - market_risk
  format: pdf
  source_url: "https://www.bis.org/bcbs/publ/d457.pdf"
  retrieved_at: ""
  license_name: "BIS terms and conditions"
  license_url: "https://www.bis.org/terms_conditions.htm"
  permissions_excerpt: "Users may download and redistribute any BIS Material for non commercial purposes"
  notes: ""

- doc_id: pdf/en/bis_bcbs_d457_note.pdf
  title: "Explanatory note on the minimum capital requirements for market risk"
  language: en
  topics:
    - frtb
    - market_risk
  format: pdf
  source_url: "https://www.bis.org/bcbs/publ/d457_note.pdf"
  retrieved_at: ""
  license_name: "BIS terms and conditions"
  license_url: "https://www.bis.org/terms_conditions.htm"
  permissions_excerpt: "Users may download and redistribute any BIS Material for non commercial purposes"
  notes: ""

- doc_id: html/en/bis_basel_framework_mar50.html
  title: "Basel Framework MAR50 Credit valuation adjustment framework"
  language: en
  topics:
    - cva
    - counterparty_risk
  format: html
  source_url: "https://www.bis.org/basel_framework/chapter/MAR/50.htm"
  retrieved_at: ""
  license_name: "BIS terms and conditions"
  license_url: "https://www.bis.org/terms_conditions.htm"
  permissions_excerpt: "Users may download and redistribute any BIS Material for non commercial purposes"
  notes: ""

- doc_id: pdf/en/bis_bcbs_d488.pdf
  title: "Credit valuation adjustment risk targeted final revisions"
  language: en
  topics:
    - cva
    - counterparty_risk
  format: pdf
  source_url: "https://www.bis.org/bcbs/publ/d488.pdf"
  retrieved_at: ""
  license_name: "BIS terms and conditions"
  license_url: "https://www.bis.org/terms_conditions.htm"
  permissions_excerpt: "Users may download and redistribute any BIS Material for non commercial purposes"
  notes: ""

- doc_id: pdf/en/imf_wp08258.pdf
  title: "Counterparty Risk in the Over The Counter Derivatives Market"
  language: en
  topics:
    - counterparty_risk
    - cva
  format: pdf
  source_url: "https://www.imf.org/external/pubs/ft/wp/2008/wp08258.pdf"
  retrieved_at: ""
  license_name: "IMF"
  license_url: ""
  permissions_excerpt: ""
  notes: "need verify IMF reuse terms; auto download failed due to server connection reset"

- doc_id: pdf/en/fsb_otc_progress_2020.pdf
  title: "OTC Derivatives Market Reforms Note on implementation progress for 2020"
  language: en
  topics:
    - otc
    - ccp
    - reporting
  format: pdf
  source_url: "https://www.fsb.org/uploads/P251120.pdf"
  retrieved_at: ""
  license_name: "FSB terms and conditions"
  license_url: "https://www.fsb.org/terms_conditions/"
  permissions_excerpt: "distribute or reproduce except for commercial purposes provided the FSB is cited as the source"
  notes: "do not modify"

- doc_id: pdf/en/fsb_otc_progress_2022.pdf
  title: "OTC Derivatives Market Reforms Implementation progress in 2022"
  language: en
  topics:
    - otc
    - ccp
    - reporting
  format: pdf
  source_url: "https://www.fsb.org/uploads/P071122.pdf"
  retrieved_at: ""
  license_name: "FSB terms and conditions"
  license_url: "https://www.fsb.org/terms_conditions/"
  permissions_excerpt: "distribute or reproduce except for commercial purposes provided the FSB is cited as the source"
  notes: "do not modify"

- doc_id: pdf/en/iosco_iq_margin_2020.pdf
  title: "Margin requirements for non centrally cleared derivatives"
  language: en
  topics:
    - margin
    - collateral
    - otc
  format: pdf
  source_url: "https://www.iosco.org/library/pubdocs/pdf/IOSCOPD651.pdf"
  retrieved_at: ""
  license_name: "IOSCO"
  license_url: ""
  permissions_excerpt: ""
  notes: "need verify IOSCO reuse terms"

- doc_id: pdf/en/iosco_iq_margin_2013_consultation.pdf
  title: "CR10 12 Margin Requirements for Non Centrally Cleared Derivatives"
  language: en
  topics:
    - margin
    - collateral
    - otc
  format: pdf
  source_url: "https://www.iosco.org/library/pubdocs/pdf/IOSCOPD387.pdf"
  retrieved_at: ""
  license_name: "IOSCO"
  license_url: ""
  permissions_excerpt: ""
  notes: "need verify IOSCO reuse terms"

- doc_id: html/en/cftc_capital_margin_page.html
  title: "CFTC Capital and Margin for Non banks"
  language: en
  topics:
    - margin
    - otc
  format: html
  source_url: "https://www.cftc.gov/LawRegulation/DoddFrankAct/Rulemakings/DF_5_CapMargin/index.htm"
  retrieved_at: ""
  license_name: "US government"
  license_url: ""
  permissions_excerpt: ""
  notes: "assume public domain but need confirm"

- doc_id: pdf/en/cftc_2020_27736_margin_uncleared_swaps.pdf
  title: "Margin Requirements for Uncleared Swaps 86 FR 229"
  language: en
  topics:
    - margin
    - otc
  format: pdf
  source_url: "https://www.cftc.gov/sites/default/files/2021/01/2020-27736.pdf"
  retrieved_at: ""
  license_name: "US government"
  license_url: ""
  permissions_excerpt: ""
  notes: "assume public domain but need confirm"

- doc_id: pdf/en/hkma_cr_g_14_en.pdf
  title: "HKMA Margin and Risk mitigation Standards for Non centrally Cleared OTC Derivatives"
  language: en
  topics:
    - margin
    - collateral
    - otc
  format: pdf
  source_url: "https://brdr.hkma.gov.hk/eng/doc-ldg/docId/getPdf/20161230-3-EN/20161230-3-EN.pdf"
  retrieved_at: ""
  license_name: "HKMA"
  license_url: ""
  permissions_excerpt: ""
  notes: "need verify HKMA reuse terms; auto download failed due to SSL certificate verification"

- doc_id: docx/en/isda_afme_frtb_position_paper.docx
  title: "AFME ISDA CRR III FRTB Position Paper"
  language: en
  topics:
    - frtb
    - market_risk
  format: docx
  source_url: "https://www.isda.org/a/wpVgE/AFME-ISDA-CRR-III-FRTB-Position-Paper.docx"
  retrieved_at: ""
  license_name: "ISDA"
  license_url: ""
  permissions_excerpt: ""
  notes: "need verify ISDA reuse terms"

- doc_id: docx/en/isda_afme_cva_position_paper.docx
  title: "AFME ISDA CRR III CVA Position Paper"
  language: en
  topics:
    - cva
    - counterparty_risk
  format: docx
  source_url: "https://www.isda.org/a/M3PgE/AFME-ISDA-CRR-III-CVA-Position-Paper.docx"
  retrieved_at: ""
  license_name: "ISDA"
  license_url: ""
  permissions_excerpt: ""
  notes: "need verify ISDA reuse terms"

- doc_id: md/en/wikipedia_frtb.md
  title: "Fundamental Review of the Trading Book"
  language: en
  topics:
    - frtb
  format: markdown
  source_url: "https://en.wikipedia.org/wiki/Fundamental_Review_of_the_Trading_Book"
  retrieved_at: ""
  license_name: "CC BY SA 4.0"
  license_url: "https://creativecommons.org/licenses/by-sa/4.0/"
  permissions_excerpt: ""
  notes: ""

- doc_id: md/en/wikipedia_cva.md
  title: "Credit valuation adjustment"
  language: en
  topics:
    - cva
  format: markdown
  source_url: "https://en.wikipedia.org/wiki/Credit_valuation_adjustment"
  retrieved_at: ""
  license_name: "CC BY SA 4.0"
  license_url: "https://creativecommons.org/licenses/by-sa/4.0/"
  permissions_excerpt: ""
  notes: ""

- doc_id: md/en/wikipedia_xva.md
  title: "X valuation adjustment"
  language: en
  topics:
    - xva
    - cva
  format: markdown
  source_url: "https://en.wikipedia.org/wiki/X_valuation_adjustment"
  retrieved_at: ""
  license_name: "CC BY SA 4.0"
  license_url: "https://creativecommons.org/licenses/by-sa/4.0/"
  permissions_excerpt: ""
  notes: ""
```
