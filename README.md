# Prometheus-SNMP-Exporter

Código utilizado no post <https://blog.rbsantosbr.me/posts/criando-metricas-personalizadas-para-o-prometheus-snmp-exporter/>

> O arquivo SMS-MIB.txt foi utilizado como referência para o post.

## Configuração

1. Clone o repositório;

2. Se necessário, adicione permissão de execução aos diretórios grafana-data e prometheus-data;

    ```bash
    chmod -R +x grafana-data
    chmod -R +x prometheus-data
    ```

3. Edite o arquivo "prometheus/prometheus.yml", altere os parâmetros de acordo com o seu exporter, e os dispositivos que serão monitorados;

    ```yaml
    global:
    scrape_interval: 15s
    scrape_timeout: 15s
    scrape_configs:
    - job_name: 'nobreaks' # Nome do job
        static_configs:
        - targets:
            - 127.0.0.1  # Dispositivo SNMP alvo
        metrics_path: /snmp
        params:
        auth: [public_v1] # Credenciais configuradas no snmp.yml
        module: [teste]   # Módulo SNMP para o dispositivo
        relabel_configs:
        - source_labels: [__address__]
            target_label: __param_target
        - source_labels: [__param_target]
            target_label: instance
        - target_label: __address__
            replacement: 127.0.0.1:9116  # IP:Porta do host onde o SNMP Exporter está configurado
    ```

4. Execute o comando ```docker compose up -d``` para subir a *stack*;

5. Para validar que os serviços da *stack* estão funcionando, acesse o prometheus através da URL "http://\<ip-do-host>:9090" > Status > Targets, e valide se o endpoint do seu exporter snmp está com status UP:

    | Endpoint | State | ... |
    | :---     | :---: | :---|
    | <http://127.0.0.1:9116/snmp> | **UP** | ... |

6. Acesse o grafana utilizando a credencial admin para usuário e senha, conecte o datasource do prometheus e crie seus dashboards conforme sua necessidade.
