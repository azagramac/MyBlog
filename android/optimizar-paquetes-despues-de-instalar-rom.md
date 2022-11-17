# 📦 Optimizar paquetes después de instalar ROM

Después de instalar una ROM la bateria esta algo perdida y necesitamos unos dias para que se "asiente" y las lecturas sean más correcta, aunque podremos hacer algún calibrado de bateria despues de una carga completa con alguna App como "[Battery Calibrator](https://f-droid.org/packages/eu.roggstar.luigithehunter.batterycalibrate/)", necesitamos optimizar los paquetes instalados. \
\
<img src="../.gitbook/assets/image (8).png" alt="" data-size="line">Informacion [https://developer.android.com/training/wearables/compose/performance?hl=es-419](https://developer.android.com/training/wearables/compose/performance?hl=es-419)\
\
Necesitaremos:\
\- Custom ROM\
\- root\
\- [Termux](instalar-termux.md)

Abrimos Termux y ejecutamos este comando

```
su -c "cmd package bg-dexopt-job"
```

En función del móvil y los paquetes instalados que tengamos, tardará más o menos en responder, cuando finalice nos devolver un "`Success`"

![](<../.gitbook/assets/image (4).png>)

Despues, usar normalmente. \
Ejecutar este comando despues de instalar todos los paquetes y Apps.&#x20;
