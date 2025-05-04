app/
├── __init__.py
├── main.py
│
├── core/
│   ├── __init__.py
│   ├── app.py
│   │   # AppFactoryInterface
│   │   - create_app(settings: Settings) -> FastAPI
│   │   - register_middleware(app: FastAPI)
│   │   - register_routes(app: FastAPI)
│   │   - setup_event_handlers(app: FastAPI)
│   │   - configure_cors(app: FastAPI)
│   │   - setup_exception_handlers(app: FastAPI)
│   │
│   ├── middleware/
│   │   ├── __init__.py
│   │   ├── auth_middleware.py
│   │   │   # AuthMiddlewareInterface
│   │   │   - authenticate_request(request: Request) -> Optional[User]
│   │   │   - extract_token(request: Request) -> Optional[str]
│   │   │   - validate_token(token: str) -> TokenPayload
│   │   │   - set_auth_context(request: Request, user: User)
│   │   │   - handle_authentication_error(error: AuthError) -> Response
│   │   │
│   │   ├── rate_limit_middleware.py
│   │   │   # RateLimitMiddlewareInterface
│   │   │   - check_rate_limit(tenant_id: str, endpoint: str) -> bool
│   │   │   - update_rate_counter(tenant_id: str, endpoint: str)
│   │   │   - get_rate_limit_headers(tenant_id: str) -> dict
│   │   │   - handle_rate_limit_exceeded() -> Response
│   │   │
│   │   ├── tenant_middleware.py
│   │   │   # TenantMiddlewareInterface
│   │   │   - extract_tenant_id(request: Request) -> str
│   │   │   - load_tenant_context(tenant_id: str) -> TenantContext
│   │   │   - set_tenant_context(request: Request, context: TenantContext)
│   │   │   - validate_tenant_subscription(tenant_id: str) -> bool
│   │   │   - handle_tenant_error(error: TenantError) -> Response
│   │   │
│   │   ├── logging_middleware.py
│   │   │   # LoggingMiddlewareInterface
│   │   │   - log_request(request: Request, context: dict)
│   │   │   - log_response(response: Response, context: dict)
│   │   │   - enrich_context(request: Request) -> dict
│   │   │   - anonymize_sensitive_data(data: dict) -> dict
│   │   │
│   │   └── error_handler.py
│   │       # ErrorHandlerInterface
│   │       - handle_exception(error: Exception, context: dict) -> Response
│   │       - format_error_response(error: Exception) -> dict
│   │       - log_error(error: Exception, context: dict)
│   │       - determine_status_code(error: Exception) -> int
│   │
│   ├── settings/
│   │   ├── __init__.py
│   │   ├── base.py
│   │   │   # BaseSettingsInterface
│   │   │   - load_environment_variables()
│   │   │   - validate_required_settings()
│   │   │   - get_database_config() -> dict
│   │   │   - get_ai_service_config() -> dict
│   │   │   - get_external_service_config(service_name: str) -> dict
│   │   │   - get_tenant_default_settings() -> dict
│   │   │   - validate_all_settings() -> bool
│   │   │
│   │   ├── development.py
│   │   ├── production.py
│   │   └── testing.py
│   │
│   └── security/
│       ├── __init__.py
│       ├── jwt_handler.py
│       ├── api_key_validator.py
│       ├── encryption.py
│       │   # EncryptionInterface
│       │   - encrypt_data(data: str, key: str) -> str
│       │   - decrypt_data(encrypted_data: str, key: str) -> str
│       │   - generate_key() -> str
│       │   - hash_password(password: str) -> str
│       │   - verify_password(password: str, hashed: str) -> bool
│       │   - encrypt_tenant_data(data: dict, tenant_id: str) -> dict
│       │
│       └── tenant_isolation.py
│
├── domain/
│   ├── __init__.py
│   ├── models/
│   │   ├── __init__.py
│   │   ├── interaction/
│   │   │   ├── __init__.py
│   │   │   ├── conversation.py
│   │   │   │   # ConversationInterface
│   │   │   │   - create_conversation(user_id: str, tenant_id: str) -> conversation_id
│   │   │   │   - add_message(conversation_id: str, message: Message)
│   │   │   │   - get_conversation_history(conversation_id: str, limit: int) -> List[Message]
│   │   │   │   - update_context(conversation_id: str, context: dict)
│   │   │   │   - check_tenant_access(conversation_id: str, tenant_id: str) -> bool
│   │   │   │   - mark_conversation_complete(conversation_id: str)
│   │   │   │
│   │   │   ├── message.py
│   │   │   │   # MessageInterface
│   │   │   │   - create_message(content: str, sender: str, message_type: MessageType)
│   │   │   │   - validate_message() -> bool
│   │   │   │   - extract_intent(content: str) -> str
│   │   │   │   - extract_entities(content: str) -> dict
│   │   │   │   - add_metadata(key: str, value: Any)
│   │   │   │   - get_message_context() -> dict
│   │   │   │
│   │   │   └── user_context.py
│   │   │
│   │   ├── workflow/
│   │   │   ├── __init__.py
│   │   │   ├── workflow_execution.py
│   │   │   ├── task.py
│   │   │   └── trigger.py
│   │   │
│   │   └── tenant/
│   │       ├── __init__.py
│   │       ├── tenant.py
│   │       ├── subscription.py
│   │       └── usage_metric.py
│   │
│   ├── value_objects/
│   │   ├── __init__.py
│   │   ├── query_intent.py
│   │   ├── confidence_score.py
│   │   ├── entity_extraction.py
│   │   └── tenant_context.py
│   │
│   ├── services/
│   │   ├── __init__.py
│   │   ├── interaction/
│   │   │   ├── __init__.py
│   │   │   ├── message_processor.py
│   │   │   │   # MessageProcessorInterface
│   │   │   │   - process_incoming_message(message: Message, context: TenantContext) -> ProcessedMessage
│   │   │   │   - determine_response_type(intent: str, tenant_rules: dict) -> ResponseType
│   │   │   │   - validate_message_content(content: str, tenant_policies: dict) -> bool
│   │   │   │   - extract_entities_with_context(content: str, context: dict) -> dict
│   │   │   │   - apply_tenant_specific_rules(message: Message, tenant_id: str) -> Message
│   │   │   │
│   │   │   ├── context_resolver.py
│   │   │   │   # ContextResolverInterface
│   │   │   │   - resolve_context(conversation_id: str, tenant_id: str) -> dict
│   │   │   │   - update_context(conversation_id: str, updates: dict, tenant_id: str)
│   │   │   │   - merge_contexts(current: dict, new: dict) -> dict
│   │   │   │   - get_relevant_history(conversation_id: str, limit: int, tenant_id: str) -> List[Message]
│   │   │   │   - extract_user_preferences(user_id: str, tenant_id: str) -> dict
│   │   │   │   - reset_context(conversation_id: str, tenant_id: str)
│   │   │   │
│   │   │   ├── conversation_manager.py
│   │   │   └── response_generator.py
│   │   │
│   │   ├── workflow/
│   │   │   ├── __init__.py
│   │   │   ├── workflow_orchestrator.py
│   │   │   │   # WorkflowOrchestratorInterface
│   │   │   │   - select_workflow(intent: str, context: dict, tenant_id: str) -> workflow_id
│   │   │   │   - prepare_workflow_input(message: Message, context: dict, tenant_rules: dict) -> dict
│   │   │   │   - execute_workflow(workflow_id: str, input_data: dict) -> execution_id
│   │   │   │   - handle_workflow_output(execution_id: str) -> Response
│   │   │   │   - validate_workflow_access(workflow_id: str, tenant_id: str) -> bool
│   │   │   │
│   │   │   ├── task_executor.py
│   │   │   │   # TaskExecutorInterface
│   │   │   │   - execute_task(task: Task, context: dict, tenant_id: str) -> TaskResult
│   │   │   │   - validate_task_prerequisites(task: Task, context: dict) -> bool
│   │   │   │   - prepare_task_input(task: Task, workflow_data: dict) -> dict
│   │   │   │   - handle_task_output(result: TaskResult) -> dict
│   │   │   │   - retry_failed_task(task: Task, attempt: int, tenant_id: str)
│   │   │   │   - get_task_status(task_id: str, tenant_id: str) -> TaskStatus
│   │   │   │
│   │   │   └── decision_engine.py
│   │   │
│   │   └── tenant/
│   │       ├── __init__.py
│   │       ├── tenant_service.py
│   │       ├── subscription_manager.py
│   │       └── usage_tracker.py
│   │
│   ├── business_rules/
│   │   ├── __init__.py
│   │   ├── query_validation.py
│   │   ├── service_selection.py
│   │   ├── response_filtering.py
│   │   └── tenant_restrictions.py
│   │
│   └── exceptions/
│       ├── __init__.py
│       ├── domain_exceptions.py
│       ├── query_exceptions.py
│       ├── workflow_exceptions.py
│       └── tenant_exceptions.py
│
├── application/
│   ├── __init__.py
│   ├── interaction/
│   │   ├── __init__.py
│   │   ├── send_message.py
│   │   │   # SendMessageUseCaseInterface
│   │   │   - execute(user_id: str, content: str, conversation_id: Optional[str], tenant_id: str)
│   │   │   - validate_input(user_id: str, content: str, tenant_id: str)
│   │   │   - check_rate_limits(user_id: str, tenant_id: str) -> bool
│   │   │   - process_and_respond(message: Message, tenant_context: TenantContext)
│   │   │   - track_usage_metrics(tenant_id: str, operation: str)
│   │   │
│   │   ├── get_interaction_history.py
│   │   ├── update_context.py
│   │   └── start_conversation.py
│   │
│   ├── workflow/
│   │   ├── __init__.py
│   │   ├── execute_workflow.py
│   │   ├── trigger_workflow.py
│   │   ├── monitor_execution.py
│   │   └── cancel_workflow.py
│   │
│   ├── tenant/
│   │   ├── __init__.py
│   │   ├── onboard_tenant.py
│   │   │   # OnboardTenantUseCaseInterface
│   │   │   - execute(tenant_data: dict) -> Tenant
│   │   │   - validate_tenant_data(data: dict) -> bool
│   │   │   - create_tenant_resources(tenant: Tenant)
│   │   │   - setup_default_configurations(tenant: Tenant)
│   │   │   - initialize_tenant_database(tenant: Tenant)
│   │   │   - send_onboarding_notification(tenant: Tenant)
│   │   │
│   │   ├── manage_subscription.py
│   │   └── track_usage.py
│   │
│   ├── command_handlers/
│   │   ├── __init__.py
│   │   └── process_query_command.py
│   │
│   └── workflow_orchestration/
│       ├── __init__.py
│       ├── workflow_coordinator.py
│       └── decision_tree.py
│
├── infrastructure/
│   ├── __init__.py
│   ├── persistence/
│   │   ├── __init__.py
│   │   ├── mongodb/
│   │   │   ├── __init__.py
│   │   │   ├── connection.py
│   │   │   ├── repositories.py
│   │   │   │   # MongoRepositoryInterface
│   │   │   │   - create_collection(name: str, tenant_id: str)
│   │   │   │   - insert_one(collection: str, document: dict, tenant_id: str)
│   │   │   │   - find_one(collection: str, query: dict, tenant_id: str) -> dict
│   │   │   │   - find_many(collection: str, query: dict, limit: int, tenant_id: str) -> List[dict]
│   │   │   │   - update_one(collection: str, query: dict, update: dict, tenant_id: str)
│   │   │   │   - delete_one(collection: str, query: dict, tenant_id: str)
│   │   │   │   - create_index(collection: str, index_spec: dict, tenant_id: str)
│   │   │   │
│   │   │   └── schema_validator.py
│   │   │
│   │   ├── postgresql/
│   │   │   ├── __init__.py
│   │   │   ├── connection.py
│   │   │   ├── migrations/
│   │   │   └── models.py
│   │   │
│   │   ├── redis/
│   │   │   ├── __init__.py
│   │   │   ├── connection.py
│   │   │   └── caching.py
│   │   │       # CacheInterface
│   │   │       - set_cache(key: str, value: Any, ttl: Optional[int], tenant_id: str)
│   │   │       - get_cache(key: str, tenant_id: str) -> Optional[Any]
│   │   │       - delete_cache(key: str, tenant_id: str)
│   │   │       - exists(key: str, tenant_id: str) -> bool
│   │   │       - increment_counter(key: str, amount: int, tenant_id: str)
│   │   │       - get_tenant_cache_stats(tenant_id: str) -> dict
│   │   │
│   │   └── vector_db/
│   │       ├── __init__.py
│   │       ├── base_vector_store.py
│   │       │   # BaseVectorStoreInterface
│   │       │   - upsert_vectors(vectors: List[Vector], metadata: List[dict], tenant_id: str)
│   │       │   - query_similar(query_vector: Vector, top_k: int, filter: dict, tenant_id: str) -> List[VectorMatch]
│   │       │   - delete_vectors(vector_ids: List[str], tenant_id: str)
│   │       │   - create_tenant_namespace(tenant_id: str)
│   │       │   - get_collection_stats(tenant_id: str) -> dict
│   │       │
│   │       ├── pinecone_store.py
│   │       └── milvus_store.py
│   │
│   ├── ai_integration/
│   │   ├── __init__.py
│   │   ├── base/
│   │   │   ├── __init__.py
│   │   │   └── model_interface.py
│   │   │       # ModelInterface
│   │   │       - initialize(config: dict, tenant_id: str)
│   │   │       - send_query(query: str, context: dict, constraints: dict) -> Response
│   │   │       - stream_response(query: str) -> AsyncIterator[str]
│   │   │       - track_usage(tenant_id: str, metrics: dict)
│   │   │       - prepare_context(history: List, preferences: dict) -> Context
│   │   │       - maintain_session(session_id: str)
│   │   │       - handle_model_error(error: Exception) -> ModelError
│   │   │
│   │   ├── mcp/
│   │   │   ├── __init__.py
│   │   │   ├── client.py
│   │   │   │   # MCPClientInterface (extends ModelInterface)
│   │   │   │   - connect_via_mcp(model_id: str, protocol_config: dict, tenant_id: str)
│   │   │   │   - send_via_mcp(query: str, context: dict, tenant_constraints: dict) -> Response
│   │   │   │   - maintain_mcp_connection(model_id: str)
│   │   │   │   - handle_mcp_streaming(query: str) -> AsyncIterator[str]
│   │   │   │   - track_token_usage(tenant_id: str, tokens_used: int)
│   │   │   │
│   │   │   ├── models.py
│   │   │   └── protocol.py
│   │   │
│   │   ├── chatgpt/
│   │   │   ├── __init__.py
│   │   │   ├── client.py
│   │   │   │   # ChatGPTClientInterface (extends ModelInterface)
│   │   │   │   - initialize_chatgpt(api_key: str, model_version: str, tenant_id: str)
│   │   │   │   - send_to_chatgpt(query: str, context: dict, tenant_id: str) -> ChatGPTResponse
│   │   │   │   - handle_chatgpt_streaming(query: str, tenant_id: str) -> AsyncIterator[str]
│   │   │   │   - manage_token_limits(query: str, tenant_id: str) -> int
│   │   │   │   - apply_tenant_customizations(prompt: str, tenant_id: str) -> str
│   │   │   │
│   │   │   └── models.py
│   │   │
│   │   ├── embeddings/
│   │   │   ├── __init__.py
│   │   │   ├── base_embedder.py
│   │   │   ├── openai_embedder.py
│   │   │   └── embedding_service.py
│   │   │
│   │   └── model_selector.py
│   │
│   ├── workflow_engine/
│   │   ├── __init__.py
│   │   ├── n8n_client.py
│   │   │   # N8NClientInterface
│   │   │   - create_workflow(workflow_definition: dict, tenant_id: str) -> workflow_id
│   │   │   - execute_workflow(workflow_id: str, input_data: dict, tenant_id: str) -> execution_id
│   │   │   - get_workflow_status(execution_id: str, tenant_id: str) -> WorkflowStatus
│   │   │   - cancel_workflow(execution_id: str, tenant_id: str)
│   │   │   - list_tenant_workflows(tenant_id: str) -> List[Workflow]
│   │   │   - update_workflow(workflow_id: str, changes: dict, tenant_id: str)
│   │   │
│   │   ├── workflow_executor.py
│   │   ├── triggers.py
│   │   └── webhooks/
│   │       ├── __init__.py
│   │       └── handlers.py
│   │
│   ├── external_services/
│   │   ├── __init__.py
│   │   ├── base/
│   │   │   ├── __init__.py
│   │   │   ├── http_client.py
│   │   │   ├── circuit_breaker.py
│   │   │   └── retry_strategy.py
│   │   │
│   │   ├── query_services/
│   │   │   ├── __init__.py
│   │   │   ├── entity_query.py
│   │   │   │   # EntityQueryInterface
│   │   │   │   - search_entities_by_keywords(keywords: List[str], filters: dict, tenant_id: str) -> List[Entity]
│   │   │   │   - search_entities_by_ids(entity_ids: List[str], tenant_id: str) -> List[Entity]
│   │   │   │   - get_entity_details(entity_id: str, tenant_id: str) -> Entity
│   │   │   │   - check_entity_availability(entity_id: str, location: str, tenant_id: str) -> bool
│   │   │   │   - get_similar_entities(entity_id: str, tenant_id: str) -> List[Entity]
│   │   │   │   - apply_tenant_filters(entities: List[Entity], tenant_id: str) -> List[Entity]
│   │   │   │
│   │   │   ├── operation_query.py
│   │   │   │   # OperationQueryInterface
│   │   │   │   - get_operation_status(operation_id: str, tenant_id: str) -> OperationStatus
│   │   │   │   - get_operation_history(user_id: str, filters: dict, tenant_id: str) -> List[Operation]
│   │   │   │   - track_operation(operation_id: str, tenant_id: str) -> TrackingInfo
│   │   │   │   - estimate_completion(operation_id: str, tenant_id: str) -> CompletionEstimate
│   │   │   │   - cancel_operation(operation_id: str, reason: str, tenant_id: str) -> CancelResult
│   │   │   │   - validate_operation_access(operation_id: str, tenant_id: str) -> bool
│   │   │   │
│   │   │   ├── resource_query.py
│   │   │   │   # ResourceQueryInterface
│   │   │   │   - check_resource_availability(resource_id: str, location: str, tenant_id: str) -> bool
│   │   │   │   - get_resource_levels(resource_id: str, tenant_id: str) -> ResourceLevel
│   │   │   │   - reserve_resource(resource_id: str, quantity: int, tenant_id: str) -> ReservationResult
│   │   │   │   - release_resource(resource_id: str, quantity: int, tenant_id: str) -> ReleaseResult
│   │   │   │   - update_resource_location(resource_id: str, location: str, tenant_id: str) -> UpdateResult
│   │   │   │
│   │   │   └── user_query.py
│   │   │
│   │   └── external_services/
│   │       ├── __init__.py
│   │       ├── user_service.py
│   │       └── notification_service.py
│   │
│   ├── event_handling/
│   │   ├── __init__.py
│   │   ├── event_bus.py
│   │   ├── publishers.py
│   │   └── subscribers.py
│   │
│   └── tenant/
│       ├── __init__.py
│       ├── tenant_manager.py
│       │   # TenantManagerInterface
│       │   - onboard_tenant(tenant_data: dict) -> Tenant
│       │   - configure_tenant_resources(tenant_id: str, resource_plan: dict)
│       │   - get_tenant_context(tenant_id: str) -> TenantContext
│       │   - update_tenant_subscription(tenant_id: str, subscription: dict)
│       │   - get_tenant_limits(tenant_id: str) -> TenantLimits
│       │   - suspend_tenant(tenant_id: str, reason: str)
│       │
│       ├── resource_allocator.py
│       └── billing_manager.py
│
├── interfaces/
│   ├── __init__.py
│   ├── api/
│   │   ├── __init__.py
│   │   ├── v1/
│   │   │   ├── __init__.py
│   │   │   ├── router.py
│   │   │   ├── interaction/
│   │   │   │   ├── __init__.py
│   │   │   │   ├── router.py
│   │   │   │   ├── endpoints/
│   │   │   │   │   ├── __init__.py
│   │   │   │   │   ├── messages.py
│   │   │   │   │   └── conversations.py
│   │   │   │   └── schemas/
│   │   │   │       ├── __init__.py
│   │   │   │       ├── message_schema.py
│   │   │   │       └── conversation_schema.py
│   │   │   │
│   │   │   ├── webhooks/
│   │   │   ├── tenant/
│   │   │   └── admin/
│   │   │       └── endpoints/
│   │   │           └── tenant_management.py
│   │   │               # API Endpoints
│   │   │               - GET /admin/tenants
│   │   │               - POST /admin/tenants
│   │   │               - PUT /admin/tenants/{tenant_id}
│   │   │               - DELETE /admin/tenants/{tenant_id}
│   │   │
│   │   └── dependencies/
│   │       ├── __init__.py
│   │       ├── auth.py
│   │       │   # AuthDependencyInterface
│   │       │   - get_current_user(token: str = Depends(oauth2_scheme)) -> User
│   │       │   - validate_user_permissions(required_permissions: List[str]) -> callable
│   │       │   - check_user_active(user: User) -> bool
│   │       │   - get_user_roles(user: User) -> List[Role]
│   │       │   - require_authentication() -> callable
│   │       │   - get_user_from_token(token: str) -> User
│   │       │
│   │       ├── tenant.py
│   │       │   # TenantDependencyInterface
│   │       │   - get_current_tenant(token: str) -> Tenant
│   │       │   - validate_tenant_access(tenant_id: str, resource: str) -> bool
│   │       │   - get_tenant_from_request(request: Request) -> Tenant
│   │       │   - apply_tenant_context(handler_function: callable) -> callable
│   │       │   - check_tenant_subscription_status(tenant_id: str) -> SubscriptionStatus
│   │       │
│   │       └── rate_limit.py
│   │
│   ├── repositories/
│   │   ├── __init__.py
│   │   ├── conversation_repository.py
│   │   │   # ConversationRepositoryInterface
│   │   │   - save_conversation(conversation: Conversation, tenant_id: str)
│   │   │   - find_by_id(conversation_id: str, tenant_id: str) -> Conversation
│   │   │   - find_by_user(user_id: str, tenant_id: str, limit: int) -> List[Conversation]
│   │   │   - update_conversation(conversation_id: str, updates: dict, tenant_id: str)
│   │   │   - delete_conversation(conversation_id: str, tenant_id: str)
│   │   │   - get_conversation_stats(tenant_id: str) -> dict
│   │   │
│   │   ├── workflow_repository.py
│   │   ├── tenant_repository.py
│   │   └── usage_repository.py
│   │
│   ├── mappers/
│   │   ├── __init__.py
│   │   ├── message_mappers.py
│   │   ├── workflow_mappers.py
│   │   └── tenant_mappers.py
│   │
│   └── channels/
│   |   ├── __init__.py
│   |   ├── base/
│   |   │   ├── __init__.py
│   |   │   ├── channel_interface.py
│   |   │   │   # ChannelInterface
│   |   │   │   - initialize(config: dict)
│   |   │   │   - send_message(recipient: str, content: str, metadata: dict) -> ChannelResponse
│   |   │   │   - receive_message(raw_data: dict) -> ChannelMessage
│   |   │   │   - validate_request(request: Request) -> bool
│   |   │   │   - get_channel_id() -> str
│   |   │   │   - get_channel_type() -> str
│   |   │   │   - get_message_limits() -> MessageLimits
│   |   │   │   - get_supported_features() -> List[ChannelFeature]
│   |   │   │   - handle_delivery_status(status_data: dict)
│   |   │   │   - handle_error(error: Exception) -> ChannelError
│   |   │   │
│   |   │   ├── response_adapter_interface.py
│   |   │   │   # ResponseAdapterInterface
│   |   │   │   - max_message_length: int
│   |   │   │   - supports_markdown: bool
│   │   │   │   - supports_rich_media: bool
│   │   │   │   - supports_quick_replies: bool
│   │   │   │   - max_quick_replies: int
│   │   │   │   - supported_media_types: List[str]
│   │   │   │   - adapt_text_response(self, response: str) -> Union[str, List[str]]
│   │   │   │   - adapt_quick_replies(self, options: List[str]) -> dict
│   │   │   │   - adapt_media_response(self, media_type: str, content: dict) -> dict
│   │   │   │   - format_entity_list(self, entities: List[dict], max_items: int) -> dict
│   │   │   │   - adapt_long_response(self, response: str) -> List[dict]
│   │   │   │   - format_error_message(self, error: str) -> dict
│   │   │   │   - apply_channel_specific_formatting(self, content: dict) -> dict
│   │   │   │
│   │   │   ├── message_transformer_interface.py
│   │   │   │   # MessageTransformerInterface
│   │   │   │   - channel_to_internal(channel_message: dict) -> InternalMessage
│   │   │   │   - internal_to_channel(internal_message: InternalMessage) -> dict
│   │   │   │   - extract_sender_id(channel_message: dict) -> str
│   │   │   │   - extract_conversation_id(channel_message: dict) -> Optional[str]
│   │   │   │   - build_response_payload(internal_response: InternalMessage) -> dict
│   │   │   │   - validate_message_format(message: dict) -> bool
│   │   │   │   - get_message_context(channel_message: dict) -> dict
│   │   │   │   - handle_special_message_types(message: dict) -> dict
│   │   │   │
│   │   │   └── webhook_handler_interface.py
│   │   │       # WebhookHandlerInterface
│   │   │       - handle_incoming_webhook(request: Request, payload: dict) -> WebhookResponse
│   │   │       - validate_signature(request: Request, payload: dict) -> bool
│   │   │       - extract_messages(payload: dict) -> List[dict]
│   │   │       - extract_status_updates(payload: dict) -> List[dict]
│   │   │       - extract_user_events(payload: dict) -> List[dict]
│   │   │       - build_verification_response(challenge: str) -> dict
│   │   │       - process_webhook_event(event_type: str, event_data: dict)
│   │   │       - get_webhook_config() -> WebhookConfig
│   │   │
│   │   ├── response_adapters/
│   │   │   ├── __init__.py
│   │   │   ├── base_adapter.py
│   │   │   ├── whatsapp_adapter.py
│   │   │   │   # WhatsAppResponseAdapter (extends ResponseAdapterInterface)
│   │   │   │   - max_message_length = 4096
│   │   │   │   - supports_markdown = True
│   │   │   │   - supports_rich_media = True
│   │   │   │   - max_quick_replies = 3
│   │   │   │   - convert_to_whatsapp_markdown(text: str) -> str
│   │   │   │   - create_button_message(buttons: List[str]) -> dict
│   │   │   │   - create_list_message(items: List[dict]) -> dict
│   │   │   │   - optimize_media_for_whatsapp(media: dict) -> dict
│   │   │   │   - handle_interactive_components(components: dict) -> dict
│   │   │   │
│   │   │   ├── facebook_adapter.py
│   │   │   ├── web_adapter.py
│   │   │   ├── sms_adapter.py
│   │   │   └── email_adapter.py
│   │   │
│   │   ├── channels/
│   │   │   ├── __init__.py
│   │   │   ├── whatsapp/
│   │   │   │   ├── __init__.py
│   │   │   │   ├── client.py
│   │   │   │   ├── webhook_handler.py
│   │   │   │   └── message_transformer.py
│   │   │   ├── facebook/
│   │   │   ├── telegram/
│   │   │   ├── sms/
│   │   │   └── email/
│   │   │
│   │   ├── routers/
│   │   │   ├── __init__.py
│   │   │   ├── base_router.py
│   │   │   ├── whatsapp_router.py
│   │   │   ├── facebook_router.py
│   │   │   └── all_channels_router.py
│   │   │
│   │   ├── bridge/
│   │   │   ├── __init__.py
│   │   │   ├── business_logic_bridge.py
│   │   │   │   # BusinessLogicBridgeInterface
│   │   │   │   - connect_to_existing_interaction_api(interaction_api_client: InteractionAPIClient)
│   │   │   │   - process_incoming_message(channel_message: ChannelMessage) -> InteractionResponse
│   │   │   │   - handle_response_from_interaction(interaction_response: InteractionResponse) -> ChannelResponse
│   │   │   │   - map_conversation_context(channel_context: dict) -> InteractionContext
│   │   │   │   - handle_error_bridge(error: InteractionError) -> ChannelError
│   │   │   │   - maintain_session_state(session_id: str)
│   │   │   │   - forward_to_workflow_engine(workflow_trigger: WorkflowTrigger)
│   │   │   │
│   │   │   ├── response_coordinator.py
│   │   │   │   # ResponseCoordinatorInterface
│   │   │   │   - coordinate_response(internal_response: InternalMessage, channel_id: str) -> ChannelResponse
│   │   │   │   - select_adapter(channel_id: str) -> ResponseAdapterInterface
│   │   │   │   - apply_tenant_rules(response: ChannelResponse, tenant_id: str) -> ChannelResponse
│   │   │   │   - handle_multi_part_response(long_response: str, channel_id: str) -> List[ChannelResponse]
│   │   │   │   - manage_response_queue(recipient: str, responses: List[ChannelResponse])
│   │   │   │   - track_delivery_status(message_id: str, channel_id: str)
│   │   │   │
│   │   │   └── context_mapper.py
│   │   │
│   │   ├── formatters/
│   │   │   ├── __init__.py
│   │   │   ├── text_splitter.py
│   │   │   │   # TextSplitterInterface
│   │   │   │   - split_by_length(text: str, max_length: int) -> List[str]
│   │   │   │   - split_by_sentences(text: str, max_length: int) -> List[str]
│   │   │   │   - split_by_paragraphs(text: str, max_length: int) -> List[str]
│   │   │   │   - smart_split(text: str, max_length: int, channel_type: str) -> List[str]
│   │   │   │   - preserve_formatting(text: str, split_points: List[int]) -> List[str]
│   │   │   │
│   │   │   ├── markdown_converters/
│   │   │   │   ├── __init__.py
│   │   │   │   ├── whatsapp_markdown.py
│   │   │   │   ├── html_to_markdown.py
│   │   │   │   └── plain_text_converter.py
│   │   │   │
│   │   │   ├── media_optimizer.py
│   │   │   └── url_shortener.py
│   │   │
│   │   └── managers/
│   │       ├── __init__.py
│   │       ├── channel_registry.py
│   │       │   # ChannelRegistryInterface
│   │       │   - register_channel(channel: ChannelInterface)
│   │       │   - unregister_channel(channel_id: str)
│   │       │   - get_channel(channel_id: str) -> ChannelInterface
│   │       │   - get_all_channels() -> List[ChannelInterface]
│   │       │   - get_active_channels() -> List[ChannelInterface]
│   │       │   - health_check_all() -> Dict[str, ChannelStatus]
│   │       │   - broadcast_to_all(message: str, exclude: Optional[List[str]])
│   │       │
│   │       ├── message_dispatcher.py
│   │       │   # MessageDispatcherInterface
│   │       │   - dispatch_to_channel(channel_id: str, message: ChannelMessage) -> DispatchResult
│   │       │   - dispatch_to_multiple(channel_ids: List[str], message: ChannelMessage) -> Dict[str, DispatchResult]
│   │       │   - queue_message(recipient: str, message: ChannelMessage, channel_id: str)
│   │       │   - process_dispatch_queue()
│   │       │   - retry_failed_dispatch(dispatch_id: str)
│   │       │   - get_dispatch_status(dispatch_id: str) -> DispatchStatus
│   │       │
│   │       └── session_manager.py
│   │
├── shared/
│   ├── __init__.py
│   ├── types/
│   │   ├── __init__.py
│   │   └── custom_types.py
│   │
│   ├── constants/
│   │   ├── __init__.py
│   │   ├── error_codes.py
│   │   ├── response_types.py
│   │   └── tenant_limits.py
│   │
│   ├── utils/
│   │   ├── __init__.py
│   │   ├── date_utils.py
│   │   ├── string_utils.py
│   │   └── json_utils.py
│   │
│   └── logging/
│       ├── __init__.py
│       ├── logger.py
│       │   # LoggerInterface
│       │   - log_info(message: str, context: dict, metadata: dict)
│       │   - log_error(error: str, context: dict, stack_trace: str)
│       │   - log_debug(details: str, context: dict)
│       │   - log_audit(event: str, actor: str, resource: str)
│       │   - add_request_context()
│       │   - add_tenant_context()
│       │   - add_trace_context()
│       │   - route_logs(level: str, destination: str)
│       │   - filter_logs(criteria: dict)
│       │
│       └── formatters.py
│
└── tests/
    ├── __init__.py
    ├── conftest.py
    ├── unit/
    │   ├── domain/
    │   ├── application/
    │   └── infrastructure/
    ├── integration/
    │   ├── api/
    │   ├── database/
    │   └── external_services/
    └── e2e/
        ├── interaction_flows/
        └── workflow_scenarios/
