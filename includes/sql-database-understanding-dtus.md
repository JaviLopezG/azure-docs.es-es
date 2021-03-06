La unidad de transacción de base de datos (DTU) es la unidad de medida en Base de datos SQL que representa la potencia relativa de bases de datos en base a una medida del mundo real: la transacción de base de datos. Usamos un conjunto de operaciones que son típicas de una solicitud de procesamiento de transacciones en línea (OLTP) y después medimos cuántas transacciones se podrían completar por segundo en condiciones de carga total (esta es la versión resumida; puede leer los detalles en [formación general de la prueba comparativa](../articles/sql-database/sql-database-benchmark-overview.md)). 

Por ejemplo, una base de datos Premium P11 con 1750 DTU proporciona una potencia de proceso de DTU 350 veces mayor que una base de datos básica con 5 DTU. 

![Introducción a Base de datos SQL: DTU de bases de datos únicas por nivel](./media/sql-database-understanding-dtus/single_db_dtus.png)

> [!NOTE]
> Si va a migrar una base de datos de SQL Server existente, puede usar una herramienta de terceros, [la calculadora de DTU de Base de datos SQL de Azure](http://dtucalculator.azurewebsites.net/), para obtener una estimación del nivel de rendimiento y el nivel de servicio que podría necesitar la base de datos en Base de datos SQL de Azure.
> 
> 

### <a name="dtu-vs-edtu"></a>DTU y eDTU
DTU para bases de datos únicas se convierte directamente en eDTU para bases de datos elásticas. Por ejemplo, una base de datos de un grupo de bases de datos elásticas de tipo Básico ofrece hasta 5 eDTU. Es el mismo rendimiento que una base de datos única de tipo Básica. La diferencia es que la base de datos flexible no consumirá ninguna eDTU del grupo hasta que tenga que hacerlo. 

![Introducción a Base de datos SQL: Grupos elásticos por nivel.](./media/sql-database-understanding-dtus/sqldb_elastic_pools.png)

Un ejemplo sencillo ayuda. Utilice un grupo de bases de datos elásticas de tipo Básico con 1000 DTU y coloque 800 bases de datos en él. Siempre y cuando se usen solamente 200 de las 800 bases de datos en un momento dado (5 DTU X 200 = 1000), no se alcanzará la capacidad del grupo y el rendimiento de la base de datos no se degradará. Este es un ejemplo simplificado para mayor claridad. Los cálculos matemáticos reales son un poco más complicados. El portal realiza los cálculos matemáticos y hace una recomendación basándose en el uso histórico de la base de datos. Consulte [Consideraciones de precio y rendimiento para un grupo de bases de datos elásticas](../articles/sql-database/sql-database-elastic-pool-guidance.md) para saber cómo funcionan las recomendaciones o para realizar los cálculos matemáticos por su cuenta. 



<!--HONumber=Jan17_HO3-->


