# ADR-006: Observabilidade como Prioridade

## Status
Aceito

## Contexto
Sistema financeiro requer monitoramento robusto para:
- Debugging eficiente de problemas em produção
- Monitoramento de performance e SLA
- Auditoria de operações financeiras
- Detecção proativa de problemas
- Compliance e rastreabilidade

## Decisão
Implementar **Observabilidade** como requisito de primeira classe:

### Três Pilares:
1. **Logs Estruturados** (JSON format)
2. **Métricas** (contadores, histogramas, gauges)
3. **Health Checks** (endpoints de saúde)

### Stack Tecnológica:
- **Logs**: PSR-3 Logger (Hyperf native)
- **Métricas**: Prometheus format (futuro)
- **Health**: Custom endpoints
- **Format**: JSON estruturado

## Consequências

### Positivas
- **Debugging**: Logs estruturados facilitam troubleshooting
- **Performance**: Identificação rápida de gargalos
- **Monitoring**: Visibilidade completa da aplicação
- **Alerting**: Base para alertas automáticos
- **Compliance**: Auditoria completa de operações
- **SLA**: Tracking de disponibilidade e performance

### Negativas
- **Overhead**: Impacto mínimo na performance
- **Storage**: Logs consomem espaço em disco
- **Complexity**: Configuração adicional

## Implementação

### 1. Logs Estruturados

#### Formato Padrão:
```json
{
  "timestamp": "2025-09-23T14:30:00.123Z",
  "level": "INFO",
  "service": "withdraw-api",
  "operation": "immediate_withdraw",
  "account_id": "550e8400-e29b-41d4-a716-446655440000",
  "amount": 150.75,
  "duration_ms": 45,
  "status": "success",
  "correlation_id": "req_123456789",
  "context": {
    "pix_type": "email",
    "pix_key": "user@example.com"
  }
}
```

#### Service Implementation:
```php
class WithdrawService
{
    private function logWithdrawAttempt(WithdrawRequest $request): void
    {
        Log::info('withdraw_attempt', [
            'operation' => 'immediate_withdraw',
            'account_id' => $request->accountId,
            'amount' => $request->amount,
            'pix_type' => $request->pixType,
            'correlation_id' => $request->correlationId
        ]);
    }
    
    private function logWithdrawSuccess(WithdrawResult $result, float $duration): void
    {
        Log::info('withdraw_success', [
            'operation' => 'immediate_withdraw',
            'account_id' => $result->accountId,
            'withdraw_id' => $result->withdrawId,
            'amount' => $result->amount,
            'duration_ms' => $duration,
            'status' => 'success'
        ]);
    }
    
    private function logWithdrawError(\Exception $e, WithdrawRequest $request): void
    {
        Log::error('withdraw_error', [
            'operation' => 'immediate_withdraw',
            'account_id' => $request->accountId,
            'amount' => $request->amount,
            'error_type' => get_class($e),
            'error_message' => $e->getMessage(),
            'status' => 'error'
        ]);
    }
}
```

### 2. Métricas Chave

#### Contadores:
- `withdraw_requests_total{method, status}`
- `withdraw_emails_sent_total{status}`
- `withdraw_concurrency_retries_total`
- `scheduled_withdraws_processed_total{status}`

#### Histogramas: 
- `withdraw_duration_seconds{operation}`
- `email_send_duration_seconds`
- `database_query_duration_seconds{query_type}`

#### Gauges:
- `account_balance_current{account_type}`
- `queue_size{queue_name}`
- `active_connections_count`

### 3. Health Checks

#### Endpoints:
```php
// GET /health
{
  "status": "healthy",
  "timestamp": "2025-09-23T14:30:00Z",
  "checks": {
    "database": "healthy",
    "queue": "healthy", 
    "email": "healthy"
  },
  "version": "1.0.0"
}

// GET /health/database  
{
  "status": "healthy",
  "response_time_ms": 12,
  "connection_pool": {
    "active": 5,
    "idle": 10,
    "max": 20
  }
}

// GET /health/queue
{
  "status": "healthy",
  "queues": {
    "email": {
      "pending": 23,
      "processing": 2,
      "failed": 0
    }
  }
}
```

#### Implementation:
```php
class HealthController
{
    public function general(): JsonResponse
    {
        $checks = [
            'database' => $this->checkDatabase(),
            'queue' => $this->checkQueue(),
            'email' => $this->checkEmail()
        ];
        
        $overallStatus = collect($checks)->every(fn($status) => $status === 'healthy') 
            ? 'healthy' 
            : 'unhealthy';
            
        return response()->json([
            'status' => $overallStatus,
            'timestamp' => now()->toISOString(),
            'checks' => $checks,
            'version' => config('app.version')
        ]);
    }
}
```

### 4. Correlation IDs

#### Request Middleware:
```php
class CorrelationIdMiddleware
{
    public function process(ServerRequestInterface $request, RequestHandlerInterface $handler): ResponseInterface
    {
        $correlationId = $request->getHeader('X-Correlation-ID')[0] 
            ?? 'req_' . Str::random(10);
            
        // Adicionar ao contexto de log
        Log::withContext(['correlation_id' => $correlationId]);
        
        $response = $handler->handle($request);
        
        return $response->withHeader('X-Correlation-ID', $correlationId);
    }
}
```

## Configuração

### Log Configuration:
```php
// config/autoload/logger.php
return [
    'default' => [
        'handler' => [
            'class' => StreamHandler::class,
            'constructor' => [
                'stream' => 'php://stdout',
                'level' => Logger::INFO,
            ],
        ],
        'formatter' => [
            'class' => JsonFormatter::class,
        ],
    ],
];
```

## Alerting Strategy (Futuro)
- **High error rate**: > 5% em 5 minutos
- **Slow response**: p95 > 1000ms por 10 minutos  
- **Queue backup**: > 1000 jobs pendentes
- **Database issues**: Health check failures
- **Disk usage**: > 80% de uso

## Alternativas Consideradas
- **APM Tools** (New Relic, DataDog): Deixados para futuro
- **Distributed Tracing**: Complexo demais para início
- **Log aggregation** (ELK Stack): Infraestrutura adicional

---
**Data**: 23/09/2025  
**Autor**: Rogerio Lamarques + GitHub Copilot  
**Revisores**: -
