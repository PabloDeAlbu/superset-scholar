# Variables comunes
COMPOSE_FILES=-f ../docker-compose.yml -f ../docker-compose-dev.yml
SERVICE=superset

# Levantar stack en dev
up-dev:
	docker compose $(COMPOSE_FILES) up -d

# Bash interactivo dentro de superset
bash:
	docker compose $(COMPOSE_FILES) exec $(SERVICE) bash

# Logs de superset
logs:
	docker compose $(COMPOSE_FILES) logs -f $(SERVICE)

# Reiniciar superset
restart:
	docker compose $(COMPOSE_FILES) restart $(SERVICE)

# Crear admin por defecto (ajustar usuario/pass según necesites)
create-admin:
	docker compose $(COMPOSE_FILES) exec $(SERVICE) superset fab create-admin \
	  --username admin \
	  --firstname Admin \
	  --lastname User \
	  --email admin@localhost \
	  --password admin

# Inicializar base de datos y assets (solo 1ra vez o tras upgrade)
init:
	docker compose $(COMPOSE_FILES) exec $(SERVICE) superset db upgrade
	docker compose $(COMPOSE_FILES) exec $(SERVICE) superset init

# Exportar datasets, charts y dashboards (al host ./superset_exports)
export-all:
	mkdir -p ./superset_exports
	docker compose $(COMPOSE_FILES) exec $(SERVICE) superset export-datasources -f /tmp/datasets.zip
	docker compose $(COMPOSE_FILES) exec $(SERVICE) superset export-charts -f /tmp/charts.zip
	docker compose $(COMPOSE_FILES) exec $(SERVICE) superset export-dashboards -f /tmp/dashboards.zip
	docker compose $(COMPOSE_FILES) cp $(SERVICE):/tmp/datasets.zip ./superset_exports/datasets.zip
	docker compose $(COMPOSE_FILES) cp $(SERVICE):/tmp/charts.zip ./superset_exports/charts.zip
	docker compose $(COMPOSE_FILES) cp $(SERVICE):/tmp/dashboards.zip ./superset_exports/dashboards.zip

# Importar datasets, charts y dashboards (desde ./superset_exports)
import-all:
	docker compose $(COMPOSE_FILES) cp ./superset_exports/datasets.zip $(SERVICE):/tmp/datasets.zip
	docker compose $(COMPOSE_FILES) cp ./superset_exports/charts.zip   $(SERVICE):/tmp/charts.zip
	docker compose $(COMPOSE_FILES) cp ./superset_exports/dashboards.zip $(SERVICE):/tmp/dashboards.zip
	docker compose $(COMPOSE_FILES) exec $(SERVICE) superset import-datasources -p /tmp/datasets.zip --overwrite
	docker compose $(COMPOSE_FILES) exec $(SERVICE) superset import-charts -p /tmp/charts.zip --overwrite
	docker compose $(COMPOSE_FILES) exec $(SERVICE) superset import-dashboards -p /tmp/dashboards.zip --overwrite
