# Worklog

---
Task ID: 1
Agent: Super Z (main agent)
Task: Build premium SolHub Green Energy solar company website (Next.js, full stack web dev)

Work Log:
- Loaded fullstack-dev skill, initialized environment (Next.js 16 + TS + Tailwind 4 + shadcn/ui scaffold)
- Installed lenis (smooth scroll) and react-countup
- Sourced 24 real solar photos via image-search skill, built contact sheets, visually verified, optimized 10 best to public/images (hero + 8 gallery + about), ~100-250KB each
- Built modular calculator engine: src/lib/calculator/{config,engine,format,proposal}.ts — bill→consumption (blended effective tariff ₹8.5 home/₹9.5 business)→annual units→capacity (95% offset target, constrained by rooftop area @90 sq.ft/kW)→generation (1450 kWh/kW/yr Maharashtra)→savings (capped at energy-only share of bill, 12% non-energy floor on remaining bill)→progressive PM-Surya Ghar subsidy (30k/kW first 2kW, 18k/kW next 1kW, cap 78k, residential only)→cost (60-70k/kW range, 65k typical)→payback 1 decimal
- Verified engine with bun scripts/test-engine.ts — ALL TESTS PASSED (subsidy slabs, dynamic sizing 2.8→4.6→7.4→9.2 kW with bill, area constraint flag, business=0 subsidy, rounding rules)
- Built full EN/HI i18n dictionary (src/lib/i18n/) with LanguageProvider + localStorage persistence; legally-sensitive strings kept English verbatim by design
- Design system in globals.css: brand tokens navy #081B2E / ocean #0F4C81 / solar #FDB813 / eco #30D158 for light+dark, glass/gradient-border/sun-glow/marquee utilities, Manrope+Inter+Noto Sans Devanagari fonts
- Built 16 components: navbar (floating glass, shrink on scroll, EN/हिं + theme toggles, Sheet mobile menu), hero (parallax image, sun glow, particles, scroll indicator), why-solhub, services (no kW ranges, per-service WhatsApp enquiry), brands (CSS marquee + disclaimer), calculator (sliders+inputs, animated result cards, Recharts cumulative savings vs net investment, dynamic WhatsApp proposal message, exact disclosure texts), projects (masonry, no filter tabs, Dialog lightbox), subsidy (slab visual + eligibility/docs/process + Check Eligibility CTA), process (6 steps, no subsidy-approval step, honest timing note), why-solar (transparent calculated stats with CountUp), about (mission/vision, no fabricated history), faq (9 Q&As with mandated warranty/cost wording), final CTA, contact (call/WhatsApp/service-area only, no fake email/address), footer (no fake social/maps), floating WhatsApp+Call buttons
- SEO: metadata + OpenGraph + JSON-LD (ElectricalContractor + FAQPage), semantic HTML, aria labels
- Fixed during verification: react-hooks/set-state-in-effect lint errors (rAF-deferred localStorage restore, CSS-driven theme icon swap, useInView-gated CountUp), CountUp target-null errors, hydration mismatch on theme button aria-label, engine savings-cap rounding, CO2 stat decimals
- Fixed stale Turbopack CSS cache (touched globals.css to force rebuild — custom brand utilities were missing from served CSS)
- Agent Browser end-to-end verification: desktop+mobile viewports, dark+light modes, EN+HI, calculator dynamics (bill 3000→8000: size 2.8→7.4 kW, subsidy 74,400→78,000; business: 6.6 kW/₹0 subsidy; area 800 caps at 8.9 kW), all 14 WhatsApp CTAs (wa.me/918669555369 with exact pre-filled messages), tel:+918669555369 links, dynamic proposal message with live values, FAQ accordion, projects lightbox, mobile menu — all working; zero fabricated claims confirmed via DOM scan

Stage Summary:
- Deliverable: complete single-page premium website at / (route src/app/page.tsx), all files under /home/z/my-project
- Key decisions: kept legally sensitive disclosures in English verbatim in both languages; brands section uses text wordmarks + "may vary" disclaimer instead of implying official dealerships; no testimonials section (no genuine reviews provided); no email/address/map (not provided)
- Engine assumptions centralized in src/lib/calculator/config.ts for future updates without UI changes

---
Task ID: calc-revision-1
Agent: Super Z (main)
Task: Remove total estimated cost & net system cost from calculator; change area assumption to 50 sq.ft per kW

Work Log:
- config.ts: rooftopAreaPerKwSqft 90 → 50 (1 kW ≈ 50 sq.ft drives capacity sizing)
- engine.ts: removed public systemCostTypical/Min/Max fields; kept netCost internally for payback estimate
- calculator.tsx: removed "Est. System Cost" + "Est. Net Cost After Subsidy" cards and chart "Net investment" ReferenceLine; recharts ReferenceLine import removed
- dictionary-en.ts / dictionary-hi.ts: removed systemCost/costRangeBefore/netCost/chartInvestment keys; chart title → "Cumulative Estimated Savings"; FAQ roof-area answer updated 80–100 → 50 sq.ft per kW for consistency
- scripts/test-engine.ts: area-constraint tests updated to 300 sq.ft → 6.0 kW cap and 100 sq.ft → ≤2.0 kW
- Verified: bun test-engine ALL PASSED, tsc src/ clean, eslint clean, dev server renders without cost cards, payback card retained

Stage Summary:
- Calculator now shows 8 outputs (size, monthly/annual gen, monthly/annual savings, remaining bill, subsidy, payback); capacity scales at 50 sq.ft per kW; default 800 sq.ft roof supports up to 16 kW

---
Task ID: seo-calculator-page
Agent: Super Z (main)
Task: Build dedicated SEO-focused /solar-calculator page (crawlable, metadata, structured data, sitemap/robots, SEO content, lead gen)

Work Log:
- site-config.ts: added SITE_URL (env-overridable canonical origin)
- layout.tsx: removed FAQPage schema (per Google guidance), added WebSite schema, SITE_URL centralized
- engine.ts: re-exposed systemCostTypical/Min/Max (cost outputs returned for dedicated page)
- proposal.ts: WhatsApp message now includes monthly/annual savings + subsidy, matches specified format
- calculator.tsx: added showCost/showHeading/dense props; "Want a detailed solar proposal?" lead line; chart extracted to savings-chart.tsx and lazy-mounted via IntersectionObserver (recharts out of initial payload); homepage teaser keeps cost hidden + links to /solar-calculator
- navbar/footer: pathname-aware anchor resolution (work from sub-pages); footer gained Solar Calculator quick link
- i18n en/hi: re-added systemCost/costRangeBefore/netCost; added proposalQuestion, fullPageLink/Note, footer.calculatorLink
- /solar-calculator: page.tsx (exact suggested title/description, canonical, OG, Twitter, WebPage+BreadcrumbList JSON-LD, single H1) + content.tsx (7 H2 sections, config-derived numbers, system-size table 1-10kW, long-tail answers, internal links to /#services /#subsidy /#projects /#about /#contact, closing WhatsApp/Call CTA, SEO-named image)
- sitemap.ts + robots.ts created; static public/robots.txt removed
- Verified: engine tests ALL PASS, tsc src/ clean, eslint clean, title/canonical/OG/H1/8×H2/JSON-LD in rendered HTML, robots.txt + sitemap.xml include page, homepage teaser intact (0 cost cards, cross-link present, 1 H1), browser test: bill 3000→10000 recalculates 2.8→9.2 kW, area 300 caps at 6.0 kW + area-limited note, business mode works, WhatsApp href carries live dynamic values to 918669555369, no mobile horizontal scroll at 390px, no console/page errors, nav from sub-page resolves /#services

Stage Summary:
- /solar-calculator is a first-class indexable page targeting solar calculator / savings / cost / system size intents for Maharashtra+India
- Homepage calculator = teaser without cost; dedicated page shows all 10 outputs incl. cost + net cost
- Content numbers auto-derive from CALCULATOR_CONFIG so article and tool never diverge
