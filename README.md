CREATE TABLE public.account_chart (
    id integer NOT NULL,
    code character varying(50) NOT NULL,
    name character varying(255) NOT NULL,
    type character varying(50) NOT NULL,
    parent_id integer,
    is_active boolean DEFAULT true,
    created_at timestamp without time zone DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE public.accounting_closing_lines (
    id integer NOT NULL,
    closing_id integer NOT NULL,
    account_id integer NOT NULL,
    account_code character varying(20) NOT NULL,
    debit numeric(14,2) DEFAULT 0 NOT NULL,
    credit numeric(14,2) DEFAULT 0 NOT NULL,
    line_type character varying(30) DEFAULT 'pl_close'::character varying NOT NULL
);

CREATE TABLE public.accounting_closings (
    id integer NOT NULL,
    period_id integer NOT NULL,
    closing_type character varying(20) DEFAULT 'monthly'::character varying NOT NULL,
    journal_entry_id integer,
    net_income numeric(14,2) DEFAULT 0 NOT NULL,
    trial_debit numeric(14,2) DEFAULT 0 NOT NULL,
    trial_credit numeric(14,2) DEFAULT 0 NOT NULL,
    status character varying(20) DEFAULT 'posted'::character varying NOT NULL,
    closed_by integer,
    closed_at timestamp without time zone DEFAULT CURRENT_TIMESTAMP,
    reason text,
    tenant_id integer
);

CREATE TABLE public.accounting_periods (
    id integer NOT NULL,
    name character varying(120) NOT NULL,
    period_type character varying(20) DEFAULT 'monthly'::character varying NOT NULL,
    start_date date NOT NULL,
    end_date date NOT NULL,
    status character varying(20) DEFAULT 'open'::character varying NOT NULL,
    tenant_id integer,
    closed_at timestamp without time zone,
    closed_by integer,
    reopened_at timestamp without time zone,
    reopened_by integer,
    reopen_reason text,
    created_by integer,
    created_at timestamp without time zone DEFAULT CURRENT_TIMESTAMP,
    company_id integer,
    CONSTRAINT accounting_periods_dates_chk CHECK ((start_date <= end_date))
);

CREATE TABLE public.accounts_receivable (
    id integer NOT NULL,
    invoice_id integer,
    customer_id integer,
    pending_amount numeric(12,2) NOT NULL,
    due_date date,
    status character varying(50) DEFAULT 'pendiente'::character varying,
    contact_id integer
);

CREATE TABLE public.activos (
    id integer NOT NULL,
    nombre character varying(150) NOT NULL,
    tipo character varying(80) NOT NULL,
    estado character varying(50) NOT NULL,
    ubicacion character varying(100),
    ultimo_mantenimiento date,
    codigo character varying(30),
    marca character varying(80),
    modelo character varying(80),
    serial character varying(80),
    fecha_compra date,
    created_at timestamp without time zone DEFAULT CURRENT_TIMESTAMP,
    archived_at timestamp without time zone,
    disponibilidad_pct numeric(6,2) DEFAULT 100,
    cliente_id integer,
    customer_id integer,
    site_id integer,
    prioridad character varying(20),
    fecha_instalacion date,
    activo boolean DEFAULT true,
    client_uuid character varying(36),
    sync_status character varying(20) DEFAULT 'synced'::character varying,
    last_synced_at timestamp without time zone,
    deleted_at timestamp without time zone,
    contact_id integer,
    parent_id integer,
    category_id integer,
    descripcion text,
    fabricante character varying(120),
    criticidad character varying(20),
    garantia_hasta date,
    vida_util_anos integer,
    costo numeric(14,2),
    moneda character varying(8) DEFAULT 'USD'::character varying,
    notas text,
    asset_kind character varying(40) DEFAULT 'activo'::character varying,
    enabled boolean DEFAULT true,
    out_of_service boolean DEFAULT false,
    classification_1 character varying(120),
    classification_2 character varying(120),
    barcode_nfc character varying(120),
    capacity character varying(80),
    maintenance_frequency character varying(80),
    supplier_name character varying(180),
    avg_daily_usage_hours numeric(10,2),
    visible_to_all boolean DEFAULT true,
    cost_center character varying(80),
    budget character varying(80),
    task_plan character varying(200),
    qr_url text,
    custom_form character varying(200),
    public_qr text,
    parent_path text,
    access_location_path text,
    part_number character varying(80),
    unit_code character varying(40),
    unit character varying(40),
    weight numeric(12,3),
    lead_time_days integer,
    serial_controlled boolean DEFAULT false,
    is_tool boolean DEFAULT false,
    is_spare_part boolean DEFAULT false,
    is_supply boolean DEFAULT false,
    asset_type character varying(80),
    codigo_area character varying(80),
    otro_1 character varying(120),
    otro_2 character varying(120),
    maintenance_plan_id integer,
    company_id integer,
    branch_id integer,
    warehouse_id integer,
    location_id integer,
    tenant_registry_id uuid,
    tenant_id integer,
    company_group_id integer,
    department_id integer
);

CREATE TABLE public.ai_queries (
    id integer NOT NULL,
    user_id integer,
    question text NOT NULL,
    detected_intent character varying(255),
    sql_generated text,
    response text,
    execution_time numeric(10,2),
    created_at timestamp without time zone DEFAULT CURRENT_TIMESTAMP,
    tools_used text,
    provider character varying(40),
    module_context character varying(80),
    record_id integer,
    response_data text,
    company_id integer
);

CREATE TABLE public.alembic_version (
    version_num character varying(128) NOT NULL
);

CREATE TABLE public.alertas (
    id integer NOT NULL,
    titulo character varying(180) NOT NULL,
    descripcion text,
    severidad character varying(20) DEFAULT 'media'::character varying,
    estado character varying(30) DEFAULT 'abierta'::character varying,
    entidad character varying(80),
    entidad_id integer,
    created_at timestamp without time zone DEFAULT CURRENT_TIMESTAMP,
    resolved_at timestamp without time zone,
    archived_at timestamp without time zone
);

CREATE TABLE public.app_settings (
    key character varying(80) NOT NULL,
    value text,
    updated_at timestamp without time zone DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE public.approval_decisions (
    id bigint NOT NULL,
    company_id integer NOT NULL,
    instance_id integer NOT NULL,
    step_id integer,
    step_sequence integer NOT NULL,
    approver_user_id integer NOT NULL,
    action character varying(20) NOT NULL,
    comment text,
    correlation_id character varying(64),
    decided_at timestamp with time zone DEFAULT now() NOT NULL
);

CREATE TABLE public.approval_definition_versions (
    id integer NOT NULL,
    company_id integer NOT NULL,
    definition_id integer NOT NULL,
    version_number integer NOT NULL,
    status character varying(20) DEFAULT 'DRAFT'::character varying NOT NULL,
    policy_type character varying(40) DEFAULT 'SEQUENTIAL'::character varying NOT NULL,
    published_at timestamp with time zone,
    created_at timestamp with time zone DEFAULT now() NOT NULL
);

CREATE TABLE public.approval_definitions (
    id integer NOT NULL,
    company_id integer NOT NULL,
    code character varying(80) NOT NULL,
    name character varying(160) NOT NULL,
    subject_type character varying(80) DEFAULT 'GENERIC'::character varying NOT NULL,
    description text,
    status character varying(20) DEFAULT 'DRAFT'::character varying NOT NULL,
    published_version_id integer,
    created_at timestamp with time zone DEFAULT now() NOT NULL
);

CREATE TABLE public.approval_history (
    id bigint NOT NULL,
    company_id integer NOT NULL,
    instance_id integer NOT NULL,
    actor_id integer,
    action character varying(40) NOT NULL,
    step_sequence integer,
    result character varying(40) NOT NULL,
    comment text,
    correlation_id character varying(64),
    occurred_at timestamp with time zone DEFAULT now() NOT NULL
);

CREATE TABLE public.approval_instances (
    id integer NOT NULL,
    public_id character varying(36) NOT NULL,
    company_id integer NOT NULL,
    definition_id integer NOT NULL,
    version_id integer NOT NULL,
    subject_type character varying(80) NOT NULL,
    subject_id character varying(64) NOT NULL,
    status character varying(20) DEFAULT 'PENDING'::character varying NOT NULL,
    current_step_sequence integer DEFAULT 1 NOT NULL,
    requester_id integer,
    correlation_id character varying(64),
    started_at timestamp with time zone DEFAULT now() NOT NULL,
    completed_at timestamp with time zone
);

CREATE TABLE public.approval_steps (
    id integer NOT NULL,
    version_id integer NOT NULL,
    sequence integer NOT NULL,
    approver_user_id integer NOT NULL,
    name character varying(160) NOT NULL
);

CREATE TABLE public.asset_categories (
    id integer NOT NULL,
    codigo character varying(40),
    nombre character varying(120) NOT NULL,
    descripcion text,
    parent_id integer,
    activo boolean DEFAULT true,
    created_at timestamp without time zone DEFAULT CURRENT_TIMESTAMP,
    archived_at timestamp without time zone
);

CREATE TABLE public.assignment_engine_audit (
    id bigint NOT NULL,
    action character varying(40) NOT NULL,
    request_id uuid,
    recommendation_id uuid,
    work_order_id integer,
    technician_user_id integer,
    score double precision,
    source character varying(40),
    user_id integer,
    company_id integer,
    tenant_registry_id uuid,
    event_id uuid,
    payload jsonb,
    created_at timestamp with time zone DEFAULT CURRENT_TIMESTAMP NOT NULL
);

CREATE TABLE public.audit_logs (
    id integer NOT NULL,
    user_id integer,
    user_email character varying(150),
    action character varying(80) NOT NULL,
    entity character varying(120),
    entity_id character varying(80),
    endpoint character varying(150),
    method character varying(10),
    path text,
    ip_address character varying(80),
    user_agent text,
    detail text,
    created_at timestamp without time zone DEFAULT CURRENT_TIMESTAMP,
    model_name character varying(80),
    record_id integer,
    field_name character varying(80),
    old_value text,
    new_value text,
    summary text,
    company_id integer
);

CREATE TABLE public.bank_accounts (
    id integer NOT NULL,
    name character varying(120) NOT NULL,
    account_number character varying(80),
    bank_name character varying(120),
    account_chart_id integer,
    currency character varying(10) DEFAULT 'PAB'::character varying,
    contact_id integer,
    is_active boolean DEFAULT true,
    created_at timestamp without time zone DEFAULT CURRENT_TIMESTAMP,
    company_id integer
);

CREATE TABLE public.bank_matching_rules (
    id integer NOT NULL,
    name character varying(120) NOT NULL,
    rule_type character varying(50) NOT NULL,
    priority integer DEFAULT 100,
    config jsonb DEFAULT '{}'::jsonb,
    confidence_weight numeric(5,2) DEFAULT 10.00,
    is_active boolean DEFAULT true,
    created_at timestamp without time zone DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE public.bank_reconciliations (
    id integer NOT NULL,
    bank_statement_line_id integer NOT NULL,
    bank_account_id integer NOT NULL,
    payment_id integer,
    invoice_id integer,
    journal_entry_id integer,
    contact_id integer,
    customer_id integer,
    matched_amount numeric(14,2) NOT NULL,
    match_confidence numeric(5,2),
    match_type character varying(30) DEFAULT 'auto'::character varying,
    status character varying(50) DEFAULT 'conciliado'::character varying,
    reconciled_by integer,
    reconciled_at timestamp without time zone DEFAULT CURRENT_TIMESTAMP,
    notes text
);

CREATE TABLE public.bank_statement_lines (
    id integer NOT NULL,
    bank_statement_id integer NOT NULL,
    line_date date NOT NULL,
    description text,
    reference character varying(255),
    amount numeric(14,2) NOT NULL,
    balance numeric(14,2),
    movement_type character varying(20) DEFAULT 'credit'::character varying,
    status character varying(50) DEFAULT 'pendiente'::character varying,
    match_confidence numeric(5,2),
    suggested_payment_id integer,
    suggested_invoice_id integer,
    suggested_journal_entry_id integer,
    duplicate_hash character varying(64),
    created_at timestamp without time zone DEFAULT CURRENT_TIMESTAMP,
    company_id integer
);

CREATE TABLE public.bank_statements (
    id integer NOT NULL,
    bank_account_id integer NOT NULL,
    statement_number character varying(50),
    period_start date,
    period_end date,
    opening_balance numeric(14,2) DEFAULT 0.00,
    closing_balance numeric(14,2) DEFAULT 0.00,
    source_format character varying(20),
    source_filename character varying(255),
    status character varying(50) DEFAULT 'importado'::character varying,
    imported_by integer,
    created_at timestamp without time zone DEFAULT CURRENT_TIMESTAMP,
    company_id integer
);

CREATE TABLE public.chatter_attachments (
    id integer NOT NULL,
    message_id integer NOT NULL,
    file_name character varying(255) NOT NULL,
    file_path text NOT NULL,
    mime_type character varying(120),
    file_size integer,
    uploaded_by integer,
    created_at timestamp without time zone DEFAULT CURRENT_TIMESTAMP,
    client_uuid character varying(36),
    sync_status character varying(20) DEFAULT 'synced'::character varying,
    last_synced_at timestamp without time zone,
    deleted_at timestamp without time zone,
    updated_at timestamp without time zone DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE public.chatter_followers (
    id integer NOT NULL,
    model_name character varying(80) NOT NULL,
    record_id integer NOT NULL,
    user_id integer NOT NULL,
    created_at timestamp without time zone DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE public.chatter_mentions (
    id integer NOT NULL,
    message_id integer NOT NULL,
    user_id integer NOT NULL,
    notified_at timestamp without time zone,
    email_sent_at timestamp without time zone,
    created_at timestamp without time zone DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE public.chatter_messages (
    id integer NOT NULL,
    model_name character varying(80) NOT NULL,
    record_id integer NOT NULL,
    message_type character varying(20) DEFAULT 'comment'::character varying NOT NULL,
    body text NOT NULL,
    author_id integer,
    author_name character varying(150),
    parent_id integer,
    created_at timestamp without time zone DEFAULT CURRENT_TIMESTAMP,
    updated_at timestamp without time zone,
    archived_at timestamp without time zone,
    client_uuid character varying(36),
    sync_status character varying(20) DEFAULT 'synced'::character varying,
    last_synced_at timestamp without time zone,
    deleted_at timestamp without time zone
);

CREATE TABLE public.checklist_template_items (
    id integer NOT NULL,
    template_id integer NOT NULL,
    texto character varying(500) NOT NULL,
    orden integer DEFAULT 0 NOT NULL,
    obligatorio boolean DEFAULT false NOT NULL,
    item_tipo character varying(20) DEFAULT 'checkbox'::character varying NOT NULL
);

CREATE TABLE public.checklist_templates (
    id integer NOT NULL,
    nombre character varying(200) NOT NULL,
    tipo character varying(40),
    activo boolean DEFAULT true NOT NULL,
    category_id integer,
    asset_id integer,
    tenant_id integer,
    created_at timestamp without time zone DEFAULT CURRENT_TIMESTAMP NOT NULL,
    updated_at timestamp without time zone DEFAULT CURRENT_TIMESTAMP NOT NULL,
    archived_at timestamp without time zone
);

CREATE TABLE public.clientes (
    id integer NOT NULL,
    codigo character varying(20),
    nombre character varying(200) NOT NULL
);

CREATE TABLE public.cmms_wo_client_signatures (
    id integer NOT NULL,
    work_order_id integer NOT NULL,
    nombre_cliente character varying(200) NOT NULL,
    file_path character varying(500) NOT NULL,
    signed_at timestamp without time zone DEFAULT CURRENT_TIMESTAMP NOT NULL,
    user_id integer NOT NULL,
    ip_address character varying(45)
);

CREATE TABLE public.cmms_wo_evidence_photos (
    id integer NOT NULL,
    work_order_id integer NOT NULL,
    user_id integer NOT NULL,
    phase character varying(20) DEFAULT 'durante'::character varying NOT NULL,
    file_path character varying(500) NOT NULL,
    observacion text,
    created_at timestamp without time zone DEFAULT CURRENT_TIMESTAMP NOT NULL,
    work_order_asset_line_id integer
);

CREATE TABLE public.cmms_wo_execution_audit (
    id integer NOT NULL,
    work_order_id integer NOT NULL,
    user_id integer NOT NULL,
    technician_id integer,
    action character varying(40) NOT NULL,
    detail jsonb,
    ip_address character varying(45),
    created_at timestamp without time zone DEFAULT CURRENT_TIMESTAMP NOT NULL
);

CREATE TABLE public.cmms_wo_execution_sessions (
    id integer NOT NULL,
    work_order_id integer NOT NULL,
    user_id integer NOT NULL,
    technician_id integer,
    status character varying(20) DEFAULT 'not_started'::character varying NOT NULL,
    started_at timestamp without time zone,
    paused_at timestamp without time zone,
    resumed_at timestamp without time zone,
    finished_at timestamp without time zone,
    worked_minutes integer DEFAULT 0 NOT NULL,
    worked_hours numeric(10,2) DEFAULT 0 NOT NULL,
    created_at timestamp without time zone DEFAULT CURRENT_TIMESTAMP NOT NULL,
    updated_at timestamp without time zone DEFAULT CURRENT_TIMESTAMP NOT NULL,
    work_order_asset_line_id integer
);

CREATE TABLE public.cmms_wo_material_consumptions (
    id integer NOT NULL,
    work_order_id integer NOT NULL,
    user_id integer NOT NULL,
    inventory_id integer,
    producto character varying(200) NOT NULL,
    cantidad numeric(12,2) DEFAULT 0 NOT NULL,
    almacen character varying(120),
    lote character varying(80),
    observacion text,
    inventory_deducted_at timestamp without time zone,
    materiales_ot_id integer,
    created_at timestamp without time zone DEFAULT CURRENT_TIMESTAMP NOT NULL,
    work_order_asset_line_id integer
);

CREATE TABLE public.commercial_document_lines (
    id integer NOT NULL,
    company_id integer NOT NULL,
    document_id integer NOT NULL,
    line_type character varying(32) NOT NULL,
    sequence integer NOT NULL,
    code character varying(64),
    description text NOT NULL,
    quantity numeric(14,4) NOT NULL,
    uom character varying(20),
    unit_price numeric(14,4) NOT NULL,
    archived_at timestamp with time zone,
    created_at timestamp with time zone DEFAULT now() NOT NULL,
    updated_at timestamp with time zone DEFAULT now() NOT NULL
);

CREATE TABLE public.commercial_document_versions (
    id integer NOT NULL,
    company_id integer NOT NULL,
    root_document_id integer NOT NULL,
    version_number integer NOT NULL,
    document_id integer NOT NULL,
    supersedes_id integer,
    is_current boolean NOT NULL,
    created_at timestamp with time zone DEFAULT now() NOT NULL
);

CREATE TABLE public.commercial_documents (
    id integer NOT NULL,
    public_id character varying(36) NOT NULL,
    company_id integer NOT NULL,
    branch_id integer,
    document_type character varying(32) NOT NULL,
    number character varying(40),
    name character varying(255) NOT NULL,
    status character varying(40) NOT NULL,
    owner_user_id integer,
    root_document_id integer,
    version_number integer NOT NULL,
    is_current boolean NOT NULL,
    supersedes_id integer,
    parent_document_id integer,
    notes_internal text,
    notes_public text,
    locked_at timestamp with time zone,
    pricing_snapshot_id integer,
    tax_snapshot_id integer,
    archived_at timestamp with time zone,
    customer_id integer,
    contact_id integer,
    opportunity_id integer,
    site_id integer,
    prepared_by_user_id integer,
    subtitle character varying(255),
    currency_code character varying(8),
    locale character varying(16),
    issued_at date,
    valid_until date,
    profile jsonb DEFAULT '{}'::jsonb NOT NULL,
    row_version integer NOT NULL,
    created_at timestamp with time zone DEFAULT now() NOT NULL,
    updated_at timestamp with time zone DEFAULT now() NOT NULL,
    created_by integer,
    updated_by integer
);

CREATE TABLE public.companies (
    id integer NOT NULL,
    parent_id integer,
    code character varying(40),
    name character varying(180) NOT NULL,
    legal_name character varying(180),
    ruc character varying(80),
    email character varying(150),
    phone character varying(50),
    address text,
    city character varying(100),
    region character varying(100),
    country character varying(100) DEFAULT 'Panamán'::character varying,
    currency character varying(10) DEFAULT 'PAB'::character varying,
    language character varying(10) DEFAULT 'es'::character varying,
    timezone character varying(80) DEFAULT 'America/Panama'::character varying,
    brand_color character varying(20) DEFAULT '#2563eb'::character varying,
    logo_path character varying(255),
    status character varying(30) DEFAULT 'activo'::character varying,
    is_default boolean DEFAULT false,
    archived_at timestamp without time zone,
    created_at timestamp without time zone DEFAULT CURRENT_TIMESTAMP,
    updated_at timestamp without time zone DEFAULT CURRENT_TIMESTAMP,
    street text,
    street2 text,
    state character varying(100),
    zip character varying(20),
    company_identifier character varying(80),
    contact_name character varying(180),
    website character varying(255),
    email_domain character varying(150),
    bounce_email character varying(150),
    catchall_email character varying(150),
    default_from_email character varying(150),
    company_group_id integer
);

CREATE TABLE public.company_app_settings (
    id integer NOT NULL,
    company_id integer NOT NULL,
    key character varying(120) NOT NULL,
    value text,
    updated_at timestamp with time zone DEFAULT CURRENT_TIMESTAMP NOT NULL
);

CREATE TABLE public.company_branches (
    id integer NOT NULL,
    company_id integer NOT NULL,
    code character varying(40),
    name character varying(180) NOT NULL,
    address text,
    city character varying(100),
    phone character varying(50),
    country character varying(100),
    timezone character varying(80),
    manager_id integer,
    status character varying(30) DEFAULT 'activo'::character varying,
    archived_at timestamp with time zone,
    created_at timestamp with time zone DEFAULT CURRENT_TIMESTAMP,
    updated_at timestamp with time zone DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE public.company_cost_centers (
    id integer NOT NULL,
    company_id integer NOT NULL,
    department_id integer,
    code character varying(40) NOT NULL,
    name character varying(180) NOT NULL,
    status character varying(30) DEFAULT 'activo'::character varying,
    archived_at timestamp with time zone,
    created_at timestamp with time zone DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE public.company_departments (
    id integer NOT NULL,
    company_id integer NOT NULL,
    branch_id integer,
    code character varying(40),
    name character varying(180) NOT NULL,
    manager_id integer,
    status character varying(30) DEFAULT 'activo'::character varying,
    archived_at timestamp with time zone,
    created_at timestamp with time zone DEFAULT CURRENT_TIMESTAMP,
    updated_at timestamp with time zone DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE public.company_groups (
    id integer NOT NULL,
    tenant_registry_id uuid,
    code character varying(64) NOT NULL,
    name character varying(200) NOT NULL,
    description text,
    status character varying(32) DEFAULT 'activo'::character varying NOT NULL,
    created_at timestamp with time zone DEFAULT CURRENT_TIMESTAMP NOT NULL,
    updated_at timestamp with time zone DEFAULT CURRENT_TIMESTAMP NOT NULL
);

CREATE TABLE public.contact_role_rel (
    contact_id integer NOT NULL,
    role character varying(30) NOT NULL
);

CREATE TABLE public.contacts (
    id integer NOT NULL,
    first_name character varying(100) NOT NULL,
    last_name character varying(100),
    "position" character varying(100),
    email character varying(150),
    phone character varying(50),
    mobile character varying(50),
    is_primary boolean DEFAULT false,
    created_at timestamp without time zone DEFAULT CURRENT_TIMESTAMP,
    updated_at timestamp without time zone DEFAULT CURRENT_TIMESTAMP,
    archived_at timestamp without time zone,
    job_title character varying(120),
    website character varying(180),
    street text,
    street2 text,
    city character varying(100),
    province character varying(100),
    zip character varying(30),
    country character varying(100),
    ruc character varying(80),
    tags text,
    sales_person character varying(120),
    sales_payment_terms character varying(120),
    sales_payment_method character varying(120),
    incoterm character varying(80),
    incoterm_location character varying(120),
    purchase_payment_terms character varying(120),
    purchase_payment_method character varying(120),
    fiscal_position character varying(120),
    receivable_account character varying(180),
    payable_account character varying(180),
    auto_invoice_policy character varying(180),
    ignore_abnormal_amount boolean DEFAULT false,
    ignore_abnormal_date boolean DEFAULT false,
    invoice_sending character varying(180),
    electronic_invoice_format character varying(120),
    peppol_id character varying(120),
    company_registry character varying(120),
    reference character varying(120),
    sector character varying(120),
    customer_location character varying(120),
    vendor_location character varying(120),
    notes text,
    logo_url text,
    partner_level character varying(120),
    ubo text,
    parent_id integer,
    is_company boolean DEFAULT false,
    name character varying(180),
    company_name character varying(180),
    contact_type character varying(40) DEFAULT 'persona'::character varying,
    vat character varying(80),
    dv character varying(20),
    address text,
    state character varying(100),
    zip_code character varying(30),
    type_document character varying(50),
    legal_name character varying(180),
    is_customer boolean DEFAULT false,
    is_supplier boolean DEFAULT false,
    is_technician boolean DEFAULT false,
    is_internal boolean DEFAULT false,
    status character varying(30) DEFAULT 'activo'::character varying,
    image_url text,
    customer_id integer,
    company_id integer,
    branch_id integer,
    department_id integer
);

CREATE TABLE public.credit_note_items (
    id integer NOT NULL,
    note_id integer,
    product_id integer,
    description text NOT NULL,
    quantity numeric(10,2) NOT NULL,
    unit_price numeric(12,2) NOT NULL,
    tax_rate numeric(5,2) DEFAULT 7.00,
    tax_amount numeric(12,2) DEFAULT 0.00,
    total numeric(12,2) NOT NULL,
    company_id integer
);

CREATE TABLE public.credit_notes (
    id integer NOT NULL,
    credit_note_number character varying(50) NOT NULL,
    invoice_id integer,
    customer_id integer,
    reason text NOT NULL,
    subtotal numeric(12,2) DEFAULT 0.00,
    tax_itbms numeric(12,2) DEFAULT 0.00,
    total numeric(12,2) DEFAULT 0.00,
    status character varying(50) DEFAULT 'borrador'::character varying,
    created_by integer,
    created_at timestamp without time zone DEFAULT CURRENT_TIMESTAMP,
    contact_id integer,
    void_reason text,
    voided_by integer,
    voided_at timestamp without time zone,
    company_id integer
);

CREATE TABLE public.crm_activities (
    id integer NOT NULL,
    opportunity_id integer,
    lead_id integer,
    customer_id integer,
    contact_id integer,
    assigned_to integer,
    activity_type character varying(40) DEFAULT 'seguimiento'::character varying,
    subject character varying(180) NOT NULL,
    notes text,
    due_date timestamp without time zone,
    status character varying(40) DEFAULT 'pendiente'::character varying,
    completed_at timestamp without time zone,
    created_at timestamp without time zone DEFAULT CURRENT_TIMESTAMP,
    company_id integer
);

CREATE TABLE public.crm_stages (
    id integer NOT NULL,
    name character varying(80) NOT NULL,
    sequence integer DEFAULT 10,
    probability integer DEFAULT 0,
    is_won boolean DEFAULT false,
    is_lost boolean DEFAULT false,
    fold boolean DEFAULT false,
    created_at timestamp without time zone DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE public.crm_tags (
    id integer NOT NULL,
    name character varying(80) NOT NULL,
    color character varying(30)
);

CREATE TABLE public.customers (
    id integer NOT NULL,
    name character varying(150) NOT NULL,
    document_type character varying(50),
    document_number character varying(80),
    email character varying(150),
    phone character varying(50),
    website character varying(150),
    industry character varying(100),
    address text,
    city character varying(100),
    country character varying(100),
    status character varying(30) DEFAULT 'activo'::character varying,
    ruc character varying(50),
    dv character varying(10),
    razon_social character varying(255),
    tipo_cliente character varying(50),
    condicion_pago character varying(50),
    cliente_legacy_id integer,
    created_at timestamp without time zone DEFAULT CURRENT_TIMESTAMP,
    updated_at timestamp without time zone DEFAULT CURRENT_TIMESTAMP,
    archived_at timestamp without time zone
);

CREATE TABLE public.dead_letter_messages (
    id bigint NOT NULL,
    public_id character varying(36) NOT NULL,
    event_id character varying(36) NOT NULL,
    event_type character varying(120) NOT NULL,
    payload jsonb DEFAULT '{}'::jsonb NOT NULL,
    reason text NOT NULL,
    source character varying(40) DEFAULT 'outbox'::character varying NOT NULL,
    company_id integer NOT NULL,
    correlation_id character varying(64) NOT NULL,
    original_outbox_id bigint,
    attempts integer DEFAULT 0 NOT NULL,
    metadata jsonb DEFAULT '{}'::jsonb NOT NULL,
    created_at timestamp with time zone DEFAULT now() NOT NULL
);

CREATE TABLE public.debit_note_items (
    id integer NOT NULL,
    note_id integer,
    product_id integer,
    description text NOT NULL,
    quantity numeric(10,2) NOT NULL,
    unit_price numeric(12,2) NOT NULL,
    tax_rate numeric(5,2) DEFAULT 7.00,
    tax_amount numeric(12,2) DEFAULT 0.00,
    total numeric(12,2) NOT NULL,
    company_id integer
);

CREATE TABLE public.debit_notes (
    id integer NOT NULL,
    debit_note_number character varying(50) NOT NULL,
    invoice_id integer,
    customer_id integer,
    reason text NOT NULL,
    subtotal numeric(12,2) DEFAULT 0.00,
    tax_itbms numeric(12,2) DEFAULT 0.00,
    total numeric(12,2) DEFAULT 0.00,
    status character varying(50) DEFAULT 'borrador'::character varying,
    created_by integer,
    created_at timestamp without time zone DEFAULT CURRENT_TIMESTAMP,
    contact_id integer,
    void_reason text,
    voided_by integer,
    voided_at timestamp without time zone,
    company_id integer
);

CREATE TABLE public.document_templates (
    id integer NOT NULL,
    company_id integer NOT NULL,
    branch_id integer,
    name character varying(120) NOT NULL,
    document_type character varying(40) NOT NULL,
    locale character varying(16) DEFAULT 'es-PA'::character varying NOT NULL,
    status character varying(20) DEFAULT 'DRAFT'::character varying NOT NULL,
    current_version_number integer DEFAULT 0 NOT NULL,
    created_at timestamp with time zone DEFAULT now() NOT NULL,
    published_at timestamp with time zone
);

CREATE TABLE public.domain_event_log (
    id bigint NOT NULL,
    event_id uuid NOT NULL,
    event_type character varying(128) NOT NULL,
    aggregate_type character varying(64) NOT NULL,
    aggregate_id character varying(64) NOT NULL,
    tenant_registry_id uuid,
    tenant_id integer,
    company_group_id integer,
    company_id integer,
    branch_id integer,
    warehouse_id integer,
    location_id integer,
    user_id integer,
    occurred_at timestamp with time zone NOT NULL,
    payload jsonb DEFAULT '{}'::jsonb NOT NULL,
    metadata jsonb DEFAULT '{}'::jsonb NOT NULL,
    created_at timestamp with time zone DEFAULT now() NOT NULL
);

CREATE TABLE public.email_logs (
    id integer NOT NULL,
    to_email character varying(150) NOT NULL,
    subject character varying(255),
    body_preview text,
    template_key character varying(80),
    related_model character varying(80),
    related_record_id integer,
    message_id integer,
    notification_id integer,
    status character varying(20) DEFAULT 'pending'::character varying NOT NULL,
    error_message text,
    sent_at timestamp without time zone,
    created_at timestamp without time zone DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE public.enterprise_webhooks (
    id integer NOT NULL,
    event_key character varying(80) NOT NULL,
    target_url text NOT NULL,
    secret character varying(120),
    http_method character varying(10) DEFAULT 'POST'::character varying,
    is_active boolean DEFAULT true,
    created_at timestamp without time zone DEFAULT CURRENT_TIMESTAMP,
    updated_at timestamp without time zone DEFAULT CURRENT_TIMESTAMP,
    tenant_id uuid,
    company_id integer
);

CREATE TABLE public.event_retry_states (
    id bigint NOT NULL,
    outbox_message_id bigint NOT NULL,
    event_id character varying(36) NOT NULL,
    attempts integer DEFAULT 0 NOT NULL,
    next_retry_at timestamp with time zone,
    last_error text,
    status character varying(20) DEFAULT 'PENDING'::character varying NOT NULL,
    updated_at timestamp with time zone DEFAULT now() NOT NULL
);

CREATE TABLE public.import_job_errors (
    id integer NOT NULL,
    import_job_id integer NOT NULL,
    row_number integer NOT NULL,
    field_name character varying(120),
    error_message text NOT NULL,
    raw_value text
);

CREATE TABLE public.import_jobs (
    id integer NOT NULL,
    module_name character varying(80) NOT NULL,
    file_name character varying(255),
    status character varying(30) DEFAULT 'pending'::character varying NOT NULL,
    total_rows integer DEFAULT 0 NOT NULL,
    success_rows integer DEFAULT 0 NOT NULL,
    error_rows integer DEFAULT 0 NOT NULL,
    created_by integer,
    created_at timestamp with time zone DEFAULT now() NOT NULL,
    company_id integer
);

CREATE TABLE public.inbox_messages (
    id bigint NOT NULL,
    public_id character varying(36) NOT NULL,
    consumer_name character varying(120) NOT NULL,
    event_id character varying(36) NOT NULL,
    event_type character varying(120) NOT NULL,
    payload jsonb DEFAULT '{}'::jsonb NOT NULL,
    envelope jsonb DEFAULT '{}'::jsonb NOT NULL,
    company_id integer NOT NULL,
    correlation_id character varying(64) NOT NULL,
    status character varying(20) DEFAULT 'PENDING'::character varying NOT NULL,
    retry_count integer DEFAULT 0 NOT NULL,
    last_error text,
    idempotency_key character varying(200) NOT NULL,
    received_at timestamp with time zone DEFAULT now() NOT NULL,
    processed_at timestamp with time zone
);

CREATE TABLE public.inspection_route_stops (
    id integer NOT NULL,
    route_id integer NOT NULL,
    asset_id integer NOT NULL,
    sequence_no integer DEFAULT 1 NOT NULL,
    estimated_minutes integer,
    plan_id integer,
    notes text,
    created_at timestamp without time zone DEFAULT CURRENT_TIMESTAMP NOT NULL
);

CREATE TABLE public.inspection_routes (
    id integer NOT NULL,
    company_id integer,
    code character varying(64) NOT NULL,
    name character varying(200) NOT NULL,
    description text,
    active boolean DEFAULT true NOT NULL,
    created_by integer,
    created_at timestamp without time zone DEFAULT CURRENT_TIMESTAMP NOT NULL,
    updated_at timestamp without time zone DEFAULT CURRENT_TIMESTAMP NOT NULL,
    archived_at timestamp without time zone
);

CREATE TABLE public.inventario (
    id integer NOT NULL,
    codigo character varying(30),
    nombre character varying(200) NOT NULL,
    descripcion text,
    categoria character varying(80),
    unidad character varying(20) DEFAULT 'und'::character varying,
    cantidad numeric(12,2) DEFAULT 0,
    stock_minimo numeric(12,2) DEFAULT 0,
    costo_unitario numeric(12,2) DEFAULT 0,
    ubicacion character varying(120),
    proveedor character varying(120),
    created_at timestamp without time zone DEFAULT CURRENT_TIMESTAMP,
    updated_at timestamp without time zone DEFAULT CURRENT_TIMESTAMP,
    archived_at timestamp without time zone,
    item_kind character varying(40) DEFAULT 'producto'::character varying,
    enabled boolean DEFAULT true,
    fabricante character varying(120),
    modelo character varying(120),
    part_number character varying(80),
    classification_1 character varying(120),
    classification_2 character varying(120),
    barcode_nfc character varying(120),
    weight numeric(12,3),
    lead_time_days integer,
    serial_controlled boolean DEFAULT false,
    visible_to_all boolean DEFAULT true,
    access_location_path text,
    qr_url text,
    custom_form character varying(200),
    otro_1 character varying(120),
    otro_2 character varying(120),
    parent_path text,
    tipo character varying(80),
    notas text,
    company_id integer,
    branch_id integer,
    warehouse_id integer,
    location_id integer,
    tenant_registry_id uuid,
    tenant_id integer,
    company_group_id integer,
    department_id integer
);

CREATE TABLE public.inventory_movements (
    id integer NOT NULL,
    inventory_id integer NOT NULL,
    work_order_id integer,
    asset_id integer,
    movement_type character varying(30) NOT NULL,
    quantity_delta numeric(14,2) NOT NULL,
    quantity_before numeric(14,2),
    quantity_after numeric(14,2),
    unit_cost numeric(14,2),
    reference character varying(120),
    notes text,
    created_by integer,
    created_at timestamp without time zone DEFAULT CURRENT_TIMESTAMP NOT NULL,
    company_id integer,
    branch_id integer,
    department_id integer,
    warehouse_id integer,
    location_id integer,
    company_group_id integer,
    tenant_registry_id uuid,
    tenant_id integer
);

CREATE TABLE public.invoice_items (
    id integer NOT NULL,
    invoice_id integer,
    product_id integer,
    description text NOT NULL,
    quantity numeric(10,2) NOT NULL,
    unit_price numeric(12,2) NOT NULL,
    tax_rate numeric(5,2) DEFAULT 7.00,
    tax_amount numeric(12,2) DEFAULT 0.00,
    total numeric(12,2) NOT NULL,
    company_id integer
);

CREATE TABLE public.invoices (
    id integer NOT NULL,
    invoice_number character varying(50) NOT NULL,
    customer_id integer,
    quote_id integer,
    subtotal numeric(12,2) DEFAULT 0.00,
    tax_itbms numeric(12,2) DEFAULT 0.00,
    discount numeric(12,2) DEFAULT 0.00,
    total numeric(12,2) DEFAULT 0.00,
    currency character varying(10) DEFAULT 'PAB'::character varying,
    payment_status character varying(50) DEFAULT 'borrador'::character varying,
    invoice_status character varying(50) DEFAULT 'borrador'::character varying,
    payment_method character varying(50),
    due_date date,
    issued_date date,
    created_by integer,
    created_at timestamp without time zone DEFAULT CURRENT_TIMESTAMP,
    contact_id integer,
    void_reason text,
    voided_by integer,
    voided_at timestamp without time zone,
    inventory_deducted_at timestamp without time zone,
    company_id integer,
    branch_id integer
);

CREATE TABLE public.job_definitions (
    id integer NOT NULL,
    company_id integer NOT NULL,
    code character varying(80) NOT NULL,
    name character varying(160) NOT NULL,
    handler_name character varying(120) NOT NULL,
    description text,
    default_max_attempts integer DEFAULT 3 NOT NULL,
    default_backoff_seconds double precision DEFAULT '1'::double precision NOT NULL,
    default_exponential_backoff boolean DEFAULT true NOT NULL,
    default_max_delay_seconds double precision DEFAULT '300'::double precision NOT NULL,
    default_priority integer DEFAULT 100 NOT NULL,
    is_active boolean DEFAULT true NOT NULL,
    created_at timestamp with time zone DEFAULT now() NOT NULL
);

CREATE TABLE public.job_history (
    id bigint NOT NULL,
    company_id integer NOT NULL,
    job_id integer NOT NULL,
    action character varying(40) NOT NULL,
    attempt integer DEFAULT 0 NOT NULL,
    worker_name character varying(80) NOT NULL,
    started_at timestamp with time zone,
    finished_at timestamp with time zone,
    duration_ms integer,
    result_payload jsonb DEFAULT '{}'::jsonb NOT NULL,
    error_message text,
    correlation_id character varying(64),
    occurred_at timestamp with time zone DEFAULT now() NOT NULL
);

CREATE TABLE public.journal_entries (
    id integer NOT NULL,
    entry_number character varying(50) NOT NULL,
    entry_date date NOT NULL,
    description text NOT NULL,
    source_type character varying(50),
    source_id integer,
    status character varying(50) DEFAULT 'borrador'::character varying,
    created_by integer,
    created_at timestamp without time zone DEFAULT CURRENT_TIMESTAMP,
    posting_kind character varying(20) DEFAULT 'post'::character varying,
    posting_version integer DEFAULT 1,
    reversed_entry_id integer,
    void_reason text,
    voided_by integer,
    bank_reconciled_at timestamp without time zone,
    bank_statement_line_id integer,
    tenant_id integer,
    company_id integer
);

CREATE TABLE public.journal_entry_lines (
    id integer NOT NULL,
    journal_entry_id integer,
    account_id integer,
    debit numeric(12,2) DEFAULT 0.00,
    credit numeric(12,2) DEFAULT 0.00,
    description text,
    customer_id integer,
    invoice_id integer,
    created_at timestamp without time zone DEFAULT CURRENT_TIMESTAMP,
    contact_id integer,
    company_id integer
);

CREATE TABLE public.leads (
    id integer NOT NULL,
    name character varying(150) NOT NULL,
    email character varying(150),
    phone character varying(50),
    company character varying(150),
    source character varying(80),
    status character varying(50) DEFAULT 'nuevo'::character varying,
    assigned_to integer,
    notes text,
    created_at timestamp without time zone DEFAULT CURRENT_TIMESTAMP,
    updated_at timestamp without time zone DEFAULT CURRENT_TIMESTAMP,
    customer_id integer,
    contact_id integer,
    priority character varying(20) DEFAULT 'Media'::character varying,
    expected_revenue numeric(14,2) DEFAULT 0,
    probability integer DEFAULT 0,
    archived_at timestamp without time zone,
    company_id integer,
    branch_id integer,
    department_id integer
);

CREATE TABLE public.license_activation_codes (
    id uuid NOT NULL,
    code character varying(64) NOT NULL,
    duration_months integer NOT NULL,
    max_users integer NOT NULL,
    max_companies integer DEFAULT 1 NOT NULL,
    plan character varying(64) DEFAULT 'standard'::character varying NOT NULL,
    status character varying(32) DEFAULT 'pending'::character varying NOT NULL,
    redeemed_tenant_id uuid,
    redeemed_at timestamp with time zone,
    redeemed_by_user_id integer,
    notes text,
    created_at timestamp with time zone DEFAULT CURRENT_TIMESTAMP NOT NULL
);

CREATE TABLE public.login_lockouts (
    email character varying(150) NOT NULL,
    failed_attempts integer DEFAULT 0 NOT NULL,
    locked_until timestamp without time zone,
    updated_at timestamp without time zone DEFAULT CURRENT_TIMESTAMP NOT NULL
);

CREATE TABLE public.maintenance_plan_assets (
    id integer NOT NULL,
    plan_id integer NOT NULL,
    asset_id integer NOT NULL,
    contact_id integer,
    company_id integer,
    is_active boolean DEFAULT true NOT NULL,
    created_at timestamp without time zone DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE public.maintenance_plan_audit (
    id integer NOT NULL,
    plan_id integer NOT NULL,
    accion character varying(40) NOT NULL,
    detalle jsonb,
    user_id integer,
    created_at timestamp without time zone DEFAULT CURRENT_TIMESTAMP NOT NULL
);

CREATE TABLE public.maintenance_plan_executions (
    id integer NOT NULL,
    plan_id integer NOT NULL,
    scheduled_date date NOT NULL,
    work_order_id integer,
    status character varying(20) DEFAULT 'generated'::character varying NOT NULL,
    created_at timestamp without time zone DEFAULT CURRENT_TIMESTAMP NOT NULL
);

CREATE TABLE public.maintenance_plan_questions (
    id integer NOT NULL,
    task_id integer NOT NULL,
    sequence integer DEFAULT 10 NOT NULL,
    description text NOT NULL,
    question_type character varying(20) DEFAULT 'text'::character varying NOT NULL,
    is_required boolean DEFAULT true NOT NULL,
    section character varying(120),
    created_at timestamp without time zone DEFAULT CURRENT_TIMESTAMP,
    updated_at timestamp without time zone DEFAULT CURRENT_TIMESTAMP,
    archived_at timestamp without time zone,
    asset_id integer,
    numeric_unit character varying(40),
    numeric_min numeric(18,4),
    numeric_max numeric(18,4),
    CONSTRAINT ck_mpq_type CHECK (((question_type)::text = ANY ((ARRAY['text'::character varying, 'yes_no'::character varying, 'numeric'::character varying])::text[])))
);

CREATE TABLE public.maintenance_plan_resources (
    id integer NOT NULL,
    plan_id integer NOT NULL,
    sequence integer DEFAULT 10 NOT NULL,
    product_name character varying(255) NOT NULL,
    resource_type character varying(40) DEFAULT 'product'::character varying NOT NULL,
    quantity numeric(18,4) DEFAULT 1 NOT NULL,
    unit character varying(40),
    is_required boolean DEFAULT false NOT NULL,
    task_id integer,
    created_at timestamp without time zone DEFAULT CURRENT_TIMESTAMP,
    updated_at timestamp without time zone DEFAULT CURRENT_TIMESTAMP,
    archived_at timestamp without time zone
);

CREATE TABLE public.maintenance_plan_tasks (
    id integer NOT NULL,
    plan_id integer NOT NULL,
    sequence integer DEFAULT 10 NOT NULL,
    name character varying(255) NOT NULL,
    description text,
    priority character varying(40) DEFAULT 'Media'::character varying,
    estimated_hours numeric(12,2) DEFAULT 0,
    is_required boolean DEFAULT true NOT NULL,
    is_active boolean DEFAULT true NOT NULL,
    created_at timestamp without time zone DEFAULT CURRENT_TIMESTAMP,
    updated_at timestamp without time zone DEFAULT CURRENT_TIMESTAMP,
    archived_at timestamp without time zone
);

CREATE TABLE public.maintenance_plans (
    id integer NOT NULL,
    codigo character varying(50) NOT NULL,
    nombre character varying(200) NOT NULL,
    activo_id integer,
    categoria_id integer,
    prioridad character varying(20) DEFAULT 'Media'::character varying NOT NULL,
    frecuencia character varying(50) NOT NULL,
    intervalo integer DEFAULT 1 NOT NULL,
    fecha_inicio date NOT NULL,
    dias_vencimiento integer DEFAULT 7 NOT NULL,
    responsable_id integer,
    descripcion text,
    activo boolean DEFAULT true NOT NULL,
    next_ot_planning_mode character varying(20) DEFAULT 'fixed'::character varying NOT NULL,
    created_at timestamp without time zone DEFAULT CURRENT_TIMESTAMP NOT NULL,
    updated_at timestamp without time zone DEFAULT CURRENT_TIMESTAMP NOT NULL,
    archived_at timestamp without time zone,
    tipo character varying(40) DEFAULT 'preventivo'::character varying,
    estado character varying(20) DEFAULT 'activo'::character varying,
    location_id integer,
    contact_id integer,
    customer_id integer,
    unidad_frecuencia character varying(40) DEFAULT 'mes'::character varying,
    frecuencia_valor integer DEFAULT 1,
    fecha_fin date,
    ultima_ejecucion timestamp without time zone,
    proxima_ejecucion date,
    duracion_estimada numeric(8,2),
    tenant_id integer,
    paused_at timestamp without time zone,
    company_id integer,
    auto_generate_next_work_order boolean DEFAULT true NOT NULL
);

CREATE TABLE public.maintenance_scheduler_runs (
    id integer NOT NULL,
    started_at timestamp without time zone DEFAULT CURRENT_TIMESTAMP NOT NULL,
    finished_at timestamp without time zone,
    created_count integer DEFAULT 0 NOT NULL,
    skipped_count integer DEFAULT 0 NOT NULL,
    status character varying(20) DEFAULT 'running'::character varying NOT NULL,
    detail jsonb
);

CREATE TABLE public.materiales_ot (
    id integer NOT NULL,
    orden_id integer NOT NULL,
    producto character varying(200),
    proveedor character varying(120),
    descripcion text,
    fecha date,
    cantidad numeric(12,2) DEFAULT 0,
    coste_unitario numeric(12,2) DEFAULT 0,
    margen_pct numeric(6,2) DEFAULT 0,
    inventory_id integer,
    almacen character varying(120),
    lote character varying(80),
    work_order_asset_line_id integer
);

CREATE TABLE public.notifications (
    id integer NOT NULL,
    user_id integer NOT NULL,
    notification_type character varying(40) DEFAULT 'mention'::character varying NOT NULL,
    title character varying(200) NOT NULL,
    body text,
    model_name character varying(80),
    record_id integer,
    record_title character varying(200),
    message_id integer,
    is_read boolean DEFAULT false NOT NULL,
    read_at timestamp without time zone,
    created_at timestamp without time zone DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE public.opportunities (
    id integer NOT NULL,
    customer_id integer,
    contact_id integer,
    title character varying(150) NOT NULL,
    description text,
    stage character varying(50) DEFAULT 'prospecto'::character varying,
    amount numeric(12,2) DEFAULT 0,
    probability integer DEFAULT 0,
    expected_close_date date,
    assigned_to integer,
    created_at timestamp without time zone DEFAULT CURRENT_TIMESTAMP,
    updated_at timestamp without time zone DEFAULT CURRENT_TIMESTAMP,
    lead_id integer,
    stage_id integer,
    expected_revenue numeric(14,2) DEFAULT 0,
    priority character varying(20) DEFAULT 'Media'::character varying,
    status character varying(40) DEFAULT 'abierta'::character varying,
    lost_reason text,
    won_date timestamp without time zone,
    archived_at timestamp without time zone,
    company_id integer
);

CREATE TABLE public.opportunity_tags (
    opportunity_id integer NOT NULL,
    tag_id integer NOT NULL
);

CREATE TABLE public.ordenes_trabajo (
    id integer NOT NULL,
    descripcion character varying(200) NOT NULL,
    tipo character varying(50) NOT NULL,
    estado character varying(50) NOT NULL,
    prioridad character varying(50) NOT NULL,
    activo_id integer,
    asignado_a character varying(100),
    fecha date DEFAULT CURRENT_DATE NOT NULL,
    codigo character varying(30),
    nombre character varying(200),
    referencia character varying(100),
    fecha_inicio timestamp without time zone,
    fecha_vencimiento timestamp without time zone,
    fecha_finalizacion timestamp without time zone,
    portes character varying(50),
    forma_pago character varying(100),
    subtipo character varying(100),
    direccion text,
    pais character varying(80),
    provincia character varying(80),
    localidad character varying(80),
    codigo_postal character varying(20),
    observaciones_direccion text,
    trabajo_realizar text,
    observaciones_trabajo text,
    cliente_id integer,
    proyecto_id integer,
    tiempo_estimado_horas numeric(8,2) DEFAULT 0,
    tiempo_real_horas numeric(8,2) DEFAULT 0,
    tarifa_hora numeric(12,2) DEFAULT 0,
    coste_mano_obra numeric(12,2) DEFAULT 0,
    descuento_pct numeric(6,2) DEFAULT 0,
    iva_pct numeric(6,2) DEFAULT 21,
    numero_factura character varying(50),
    observaciones_facturacion text,
    subtotal numeric(12,2) DEFAULT 0,
    total_factura numeric(12,2) DEFAULT 0,
    archived_at timestamp without time zone,
    updated_at timestamp without time zone,
    invoice_id integer,
    frequency character varying(100),
    next_ot_planning_mode character varying(20) DEFAULT 'fixed'::character varying,
    parent_ot_id integer,
    plan_id integer,
    solicitud_id integer,
    client_uuid character varying(36),
    sync_status character varying(20) DEFAULT 'synced'::character varying,
    last_synced_at timestamp without time zone,
    deleted_at timestamp without time zone,
    created_at timestamp without time zone DEFAULT CURRENT_TIMESTAMP,
    contact_id integer,
    customer_id integer,
    location_id integer,
    tenant_id integer,
    assigned_to integer,
    titulo character varying(200),
    planned_start timestamp without time zone,
    planned_end timestamp without time zone,
    real_start timestamp without time zone,
    real_end timestamp without time zone,
    estimated_hours numeric(8,2),
    worked_hours numeric(8,2),
    cost numeric(14,2),
    currency character varying(8) DEFAULT 'USD'::character varying,
    notes text,
    company_id integer,
    child_sequence integer,
    next_work_order_id integer,
    cycle_number integer,
    scheduled_occurrence_date date,
    generation_source character varying(40),
    execution_mode character varying(32) DEFAULT 'single'::character varying NOT NULL,
    inspection_route_id integer,
    branch_id integer,
    department_id integer,
    warehouse_id integer,
    tenant_registry_id uuid,
    company_group_id integer,
    stage_code character varying
);
