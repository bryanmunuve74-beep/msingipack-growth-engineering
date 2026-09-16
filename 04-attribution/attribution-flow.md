# Attribution Flow

## 1. Overview
From the moment a user clicks a Meta advertisement until they eventually purchase a course, a complex network of data transfers must occur to maintain attribution. This document serves as the master map, explaining how the mechanisms detailed in previous documents work together as a cohesive, end-to-end system.

## 2. Attribution Architecture

```text
                  META AD
                     │
                     ▼
               User clicks
                     │
                     ▼
            Landing URL
        ┌────────────┴────────────┐
        │                         │
     UTM data                  FBCLID
        │                         │
        └────────────┬────────────┘
                     ▼
              Capture Layer
                     │
                     ▼
        Attribution Persistence
                     │
             .msingipack.com
                     │
                     ▼
             Marketing Site
                     │
                     ▼
          academy.msingipack.com
                     │
                     ▼
                User action
                     │
                     ▼
             Registration
                     │
                     ▼
                Purchase
                     │
                     ▼
        Attribution available
        during conversion
