1. **Validasi**:
    - Seharusnya validasi tidak di tulis rule nya di controller tapi di Request.
    - Bukan seperti ini:
    ```php
    Public function store(StoreExamRequest $request){
        $data = $request->except(keys: '_token');
        $this->exam->createExam($data);
        return back();
    }
    ```

    - Seperti ini:
    ```php
    Public function store(StoreExamRequest $request){
        $data = $request->validated();
        $this->exam->createExam($data);
        return back();
    }
    ```
    - Lalu tuliskan rule validasi di `StoreExamRequest`.

2. **Enkapsulasi**:
    - Alih alih langsung menggunakan Eloquent untuk memanipulasi data di controller, Lebih baik memisahkan kedalam sebuah service. Karena Eloquent `exam` bisa merefrensi dengan banyak hal dan untuk menghindari itu lebih baik di buat service untuk menangani pembuatan data.
    # X
    ```php
        public ExamService $exam;
        public function __construct(ExamService $exam)
        {
            $this->exam = $exam;
            $this->authorizeResource(Exam::class, 'exam');
        }
        public function store(StoreExamRequest $request): RedirectResponse
        {
            $data = $request->validated;
            $this->exam->createExam($data);
            return back();
        }
    ```
    # O
    ```php
        //CONTROLLER
        public ExamService $exam;
        public function __construct(ExamService $examService)
        {
            $this->examService = $examService;
            $this->authorizeResource(Exam::class, 'exam');
        }

        public function store(StoreExamRequest $request): RedirectResponse
        {
            $data = $request->validated;
            $this->examService->createExam($data);
            return back();
        }

        //SERVICE
        public function createExam($records)
        {
            $exam = Exam::create([
                'name'        => $records['name'],
                'description' => $records['description'],
                'semester_id' => $records['semester_id'],
                'start_date'  => $records['start_date'],
                'stop_date'   => $records['stop_date'],
            ]);

            return $exam;
        }
    ```
2. **Enkapsulasi**:
    - Alih alih langsung menggunakan Eloquent untuk memanipulasi data di controller, Lebih baik memisahkan kedalam sebuah service. Karena Eloquent `exam` bisa merefrensi dengan banyak hal dan untuk menghindari itu lebih baik di buat service untuk menangani pembuatan data.
    # X
    ```php
        public ExamService $exam;
        public function __construct(ExamService $exam)
        {
            $this->exam = $exam;
            $this->authorizeResource(Exam::class, 'exam');
        }
        public function store(StoreExamRequest $request): RedirectResponse
        {
            $data = $request->validated;
            $this->exam->createExam($data);
            return back();
        }
    ```
    # O
    ```php
        //CONTROLLER
        public ExamService $exam;
        public function __construct(ExamService $examService)
        {
            $this->examService = $examService;
            $this->authorizeResource(Exam::class, 'exam');
        }

        public function store(StoreExamRequest $request): RedirectResponse
        {
            $data = $request->validated;
            $this->examService->createExam($data);
            return back();
        }

        //SERVICE
        public function createExam($records)
        {
            $exam = Exam::create([
                'name'        => $records['name'],
                'description' => $records['description'],
                'semester_id' => $records['semester_id'],
                'start_date'  => $records['start_date'],
                'stop_date'   => $records['stop_date'],
            ]);

            return $exam;
        }
    ```
3. **Fungsi dari service**:
    - Service seharusnya tidak mengirimkan session, Service dibuat untuk menampilkan sebuah record, Boolean atau pesan. Session seharusnya di handel oleh Controller.

4. **Jangan over engineering part 1**:
    - Jika service hanya untuk menjalankan 1 proses eloquent kemudian service hanya di panggil oleh controller 1 kali, lebih baik di sederhanakan.
    # X
    ```php
        //CONTROLLER
        public ExamService $exam;
        public function __construct(ExamService $examService)
        {
            $this->examService = $examService;
            $this->authorizeResource(Exam::class, 'exam');
        }

        public function store(StoreExamRequest $request): RedirectResponse
        {
            $data = $request->validated();
            $this->examService->createExam($data);
            return back();
        }

        //SERVICE
        public function createExam($records)
        {
            $exam = Exam::create([
                'name'        => $records['name'],
                'description' => $records['description'],
                'semester_id' => $records['semester_id'],
                'start_date'  => $records['start_date'],
                'stop_date'   => $records['stop_date'],
            ]);

            return $exam;
        }
    ```
    # O
    ```php
        //CONTROLLER
        public ExamService $exam;
        public function __construct(ExamService $examService)
        {
            $this->examService = $examService;
            $this->authorizeResource(Exam::class, 'exam');
        }

        public function store(StoreExamRequest $request): RedirectResponse
        {
            Exam::Create($request->validated());
            session()->flash('success', 'Exam created successfully');
            return back();
        }

        //SERVICE
        
    ```
    - Jika sudah menggunakan validasi request, Maka melakukan insert menggunakan Array sudah tidak diperlukan karena data yang datang sudah di validasi oleh rule di request.

