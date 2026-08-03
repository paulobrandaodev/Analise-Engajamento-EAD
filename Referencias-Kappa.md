Opitz, J. (2024). "A Closer Look at Classification Evaluation Metrics and a Critical Reflection of Common Evaluation Practice." Transactions of the ACL (TACL), 12, 820–836. DOI: 10.1162/tacl_a_00675 · arXiv:2404.16958

É a referência mais direta para o seu caso: o artigo é um panorama estruturado das propriedades de Acurácia, macro-Precisão, macro-Recall, macro-F1, Kappa e MCC, derivadas dos conceitos de bias e prevalence. A versão de preprint chegou a se chamar "From Bias and Prevalence to Macro F1, Kappa, and MCC".
Serve para justificar por escolha explícita, não por hábito — o argumento central do artigo é que a maioria dos trabalhos escolhe "macro" sem dizer o que espera da métrica, e que essa escolha altera o ranking dos sistemas. Citá-lo blinda a sua Seção de metodologia.
Farhadpour, S.; Warner, T. A.; Maxwell, A. E. (2024). "Selecting and Interpreting Multiclass Loss and Accuracy Assessment Metrics for Classifications with Class Imbalance: Guidance and Best Practices." Remote Sensing, 16(3), 533. DOI: 10.3390/rs16030533 (open access)

Guia prático de best practices para classificação multiclasse desbalanceada — exatamente o seu cenário (níveis 0–3 do DAiSEE com 94% em 2+3). Discute sensibilidade das métricas macro-averaged à prevalência das classes.
⚠️ Use com honestidade: este artigo argumenta que macro-F1 (por herdar a precisão, sensível à prevalência) é menos estável que macro-Recall / balanced accuracy. Isso não invalida seu plano — fortalece, se você reportar macro-F1 e macro-recall junto com kappa. É um bom parágrafo de "limitações da métrica" na mini-qualificação.
2. Justificativa específica do Cohen's kappa
De la Cruz Huayanay, A.; Bazán, J. L.; Russo, C. M. (2024). "Performance of evaluation metrics for classification in imbalanced data." Computational Statistics. DOI: 10.1007/s00180-024-01539-5

A citação mais forte que você tem para o kappa. Estudo de simulação extensivo com 12 métricas em dados desbalanceados: MCC, G-Mean e Cohen's kappa apresentam desempenho consistentemente favorável na seleção de modelos, enquanto AUC e Acurácia têm desempenho ruim em todos os cenários estudados.
Isso justifica literalmente a sua frase do plano: "não apenas em acurácia (que é enganosa dado o desbalanceamento de 94%)".
Wong, T.-T.; Chung, P.-C. (2025). "A consistency analysis on four evaluation metrics for classifying imbalanced data." Knowledge and Information Systems, 67(11), 10639–10656. DOI: 10.1007/s10115-025-02544-w

Complemento de 2025 sobre consistência entre métricas em dados desbalanceados — útil para sustentar o reporte de múltiplas métricas em conjunto em vez de uma só.
Warrens, M. J.; de Raadt, A.; Bosker, R. J.; Kiers, H. A. L. (2025). "Weighted Kappa for Interobserver Agreement and Missing Data." Machine Learning and Knowledge Extraction, 7(1), 18. DOI: 10.3390/make7010018 (open access)

Referência para o kappa ponderado (quadratic weighted kappa), que é o que você deveria usar dado que Engagement é ordinal (0<1<2<3): ele penaliza mais erros distantes (prever 3 quando é 0) do que erros adjacentes. Warrens é uma das autoridades metodológicas no assunto.
3. Uso no seu domínio (engajamento discente)
Almuniri, I. S. et al. (2026). "Beyond peak accuracy: a stability-centric framework for reliable multimodal student engagement assessment." Scientific Reports, 16, 5. DOI: 10.1038/s41598-025-31215-7 (open access)

Precedente de domínio quase ideal. Reporta acurácia, macro-F1, balanced accuracy e Cohen's kappa para avaliação de engajamento discente, e justifica macro-F1 exatamente pelo seu motivo: atribui peso igual a cada classe, permitindo que os estados minoritários influenciem a avaliação apesar da distribuição desbalanceada (no dataset deles, 71,7% em uma única classe — desbalanceamento análogo ao do DAiSEE).
O título ("Beyond peak accuracy") é um achado retórico para a sua introdução.
Goyal et al. (2026). "Zero-Shot Vision-Language Models for Classroom Engagement Recognition." arXiv:2606.21861 — já está na Seção 3 do seu PLANO_EXECUCAO_MINIQUALIFICACAO.md. Vale reaproveitar aqui: é o caso empírico de que kappa < 0,10 revelou colapso de classe que a acurácia teria escondido. É a sua evidência de que a métrica detecta o modo de falha que você quer evitar.

4. Ressalva que você deve declarar
Existe crítica consolidada ao kappa como métrica única (Delgado & Tibau, PLOS ONE, 2019, DOI: 10.1371/journal.pone.0222916 — pré-2024, mas é a crítica canônica) e o próprio Farhadpour et al. (2024) faz ressalvas ao macro-F1. A postura defensável na banca é: nenhuma métrica isolada, mas um conjunto pequeno e motivado — macro-F1 (peso igual por classe) + kappa quadrático ponderado (correção por acaso + ordinalidade) + recall da classe minoritária, todas com IC 95% por bootstrap, como você já previu na Seção 2 do plano.

BibTeX

@article{opitz2024closer,
  author  = {Opitz, Juri},
  title   = {A Closer Look at Classification Evaluation Metrics and a
             Critical Reflection of Common Evaluation Practice},
  journal = {Transactions of the Association for Computational Linguistics},
  volume  = {12}, pages = {820--836}, year = {2024},
  doi     = {10.1162/tacl_a_00675}
}

@article{farhadpour2024selecting,
  author  = {Farhadpour, Sarah and Warner, Timothy A. and Maxwell, Aaron E.},
  title   = {Selecting and Interpreting Multiclass Loss and Accuracy Assessment
             Metrics for Classifications with Class Imbalance: Guidance and Best Practices},
  journal = {Remote Sensing}, volume = {16}, number = {3}, pages = {533},
  year    = {2024}, doi = {10.3390/rs16030533}
}

@article{delacruz2024performance,
  author  = {de la Cruz Huayanay, Alex and Baz{\'a}n, Jorge L. and Russo, Cibele M.},
  title   = {Performance of evaluation metrics for classification in imbalanced data},
  journal = {Computational Statistics}, year = {2024},
  doi     = {10.1007/s00180-024-01539-5}
}

@article{wong2025consistency,
  author  = {Wong, Tzu-Tsung and Chung, Pei-Chen},
  title   = {A consistency analysis on four evaluation metrics for classifying imbalanced data},
  journal = {Knowledge and Information Systems},
  volume  = {67}, number = {11}, pages = {10639--10656},
  year    = {2025}, doi = {10.1007/s10115-025-02544-w}
}

@article{warrens2025weighted,
  author  = {Warrens, Matthijs J. and de Raadt, Alexandra and Bosker, Roel J.
             and Kiers, Henk A. L.},
  title   = {Weighted Kappa for Interobserver Agreement and Missing Data},
  journal = {Machine Learning and Knowledge Extraction},
  volume  = {7}, number = {1}, pages = {18}, year = {2025},
  doi     = {10.3390/make7010018}
}

@article{almuniri2026beyond,
  author  = {Almuniri, Ibrahim S. and Alhussian, Hitham and Aziz, Norshakirah
             and Khairy, Sara O. F. and AlAbri, Ahmed S. and Jarallah, Zahra F.
             and Yahaya, Suraya and Adamu, Suleiman},
  title   = {Beyond peak accuracy: a stability-centric framework for reliable
             multimodal student engagement assessment},
  journal = {Scientific Reports}, volume = {16}, pages = {5}, year = {2026},
  doi     = {10.1038/s41598-025-31215-7}
}
Parágrafo pronto para a mini-qualificação
A distribuição de rótulos do DAiSEE concentra 94,2% das amostras nos níveis 2 e 3 de engajamento, de modo que um classificador trivial que sempre prediz a classe majoritária alcança ~49,5% de acurácia sem qualquer capacidade discriminativa. Por isso, a acurácia é rejeitada como métrica primária: estudos de simulação recentes mostram que, sob desbalanceamento, a acurácia e a AUC apresentam desempenho consistentemente ruim na seleção de modelos, enquanto o coeficiente kappa de Cohen figura entre as métricas mais confiáveis (DE LA CRUZ HUAYANAY; BAZÁN; RUSSO, 2024). Adota-se, portanto, o macro-F1 — que atribui peso igual a cada classe e impede que os níveis minoritários sejam ofuscados pela classe dominante (OPITZ, 2024; ALMUNIRI et al., 2026) — em conjunto com o kappa quadrático ponderado, que corrige a concordância esperada por acaso e respeita a natureza ordinal da escala de engajamento (WARRENS et al., 2025). Reconhece-se que métricas macro-averaged baseadas em precisão são sensíveis à prevalência das classes (FARHADPOUR; WARNER; MAXWELL, 2024); mitiga-se essa limitação reportando também o recall da classe minoritária e intervalos de confiança de 95% obtidos por bootstrap.

Fontes:

Opitz 2024 — TACL / arXiv:2404.16958 · ACL Anthology
Farhadpour, Warner & Maxwell 2024 — Remote Sensing 16(3):533
De la Cruz Huayanay, Bazán & Russo 2024 — Computational Statistics
Wong & Chung 2025 — Knowledge and Information Systems
Warrens et al. 2025 — Machine Learning and Knowledge Extraction 7(1):18
Almuniri et al. 2026 — Scientific Reports
Delgado & Tibau 2019 — PLOS ONE (crítica ao kappa)