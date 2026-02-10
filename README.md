# TubeSage : Real-Time YouTube Surfing Assistant

A Chrome extension that augments **YouTube** with real-time indicators to help users **avoid low-value, manipulative, or clickbait videos before wasting time**.

---

## **Product Philosophy**

This product is not about *analysis*.

It’s about **interruption prevention**.

The best video is the one you never had to click to realize it was useless.

## **Key UX Surfaces**

### **1\. Browse Feed (Home / Subscriptions / Search)**

The extension modifies the UI to add **lightweight visual indicators** to each video card.

#### **Indicators (Examples)**

* 🟢 **Likely High Value**  
* 🟡 **Mixed / Promotional**  
* 🔴 **Likely Clickbait / Low Density**

## **Monorepo Structure**

/apps

  /extension

  /api

  /worker

/packages

  /evaluation

  /transcripts

  /shared

  /db

---

### Details
More info is in `docs/TubeSage_Technical_PRD.md`