# LIV Dashboard — contexto para Claude Code

## Propósito
Dashboard financiero interno de LIV Administración SpA.
Acceso protegido por contraseña (hash SHA-256 en index.html).

## Estructura del repo
```
liv-dashboard/
├── CLAUDE.md                         ← este archivo
├── index.html                        ← dashboard (no modificar auth ni hash)
├── data.json                         ← generado automáticamente, no editar a mano
├── data/
│   ├── LIV_Contabilidad_2026.xlsx    ← FUENTE PRINCIPAL (subir aquí cada mes)
│   ├── RCV_COMPRA_*.csv              ← registro de compras SII (opcional)
│   ├── RCV_VENTA_*.csv               ← registro de ventas SII (opcional)
│   └── f29_*.pdf                     ← certificados F29 (referencia, no se parsea)
├── scripts/
│   └── parse_finances.py             ← genera data.json desde el Excel
└── .github/
    └── workflows/
        └── update_dashboard.yml      ← corre parse_finances.py al hacer push a /data/
```

## Fuente de datos principal
- Archivo: `data/LIV_Contabilidad_2026.xlsx`
- Hoja relevante: `Estado de Resultados`
- Columnas A-B: código de cuenta (4xxx/5xxx) y nombre
- Columnas C en adelante: montos por período (encabezado = fecha o nombre del mes)

## Plan de cuentas
- Cuentas `4xxx` → ingresos (clasificar por tipo de cliente según CUENTA_CLIENTE en parse_finances.py)
- Cuentas `5xxx` → gastos operacionales
- Cuentas `5xxx_diferido` → gastos devengados sin factura aún (Microsoft/Anthropic pendientes tipo 46)

## F29
Los certificados PDF no se parsean automáticamente.
Los datos se ingresan manualmente en el diccionario `PERIODOS_F29` dentro de `parse_finances.py`.

## Flujo mensual
1. Cerrar el mes en LIV_Contabilidad_2026.xlsx
2. Subir el Excel a la carpeta `/data/` del repo
3. GitHub Actions corre parse_finances.py automáticamente
4. data.json se actualiza y el dashboard refleja el nuevo mes

## Restricciones importantes
- No modificar el hash de contraseña en index.html sin actualizar la documentación
- No modificar data.json manualmente — siempre regenerar con parse_finances.py
- El Excel debe mantener el nombre de hoja `Estado de Resultados` exactamente
